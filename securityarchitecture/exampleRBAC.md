---
title: Example Architecture - RBAC outline
short_title: Security Architecture RBAC Example
---

This section presents a role-based access control (RBAC) example for a fictitious system, “SkyLink API Gateway”.

It illustrates how an RBAC design can be developed and applied within a real-world system context, providing a practical reference for implementing authorisation controls as part of a Security by Design approach.


## Overview

SkyLink implements Role-Based Access Control (RBAC) following the **Security by Design** principle of **Least Privilege**. Each role has only the minimum permissions required for its function.

## Roles

| Role | Description | Use Case |
|------|-------------|----------|
| `aircraft_standard` | Default aircraft role | Basic operations (weather, telemetry write) |
| `aircraft_premium` | Premium aircraft | Extended access (+ contacts) |
| `ground_control` | Ground control station | Monitoring operations (read-only) |
| `maintenance` | Maintenance personnel | Diagnostic access |
| `admin` | System administrator | Full access |

## Permissions

| Permission | Description | Resources |
|------------|-------------|-----------|
| `weather:read` | Access weather data | GET /weather/current |
| `contacts:read` | Access contacts data | GET /contacts/ |
| `telemetry:write` | Ingest telemetry | POST /telemetry/ingest |
| `telemetry:read` | Read telemetry | GET /telemetry/events/* |
| `config:read` | Read configuration | Future endpoint |
| `config:write` | Modify configuration | Future endpoint |
| `audit:read` | Read audit logs | Future endpoint |

## Role-Permission Matrix

| Role | weather:read | contacts:read | telemetry:write | telemetry:read | config:read | config:write | audit:read |
|------|:------------:|:-------------:|:---------------:|:--------------:|:-----------:|:------------:|:----------:|
| aircraft_standard | ✅ | ❌ | ✅ | ❌ | ❌ | ❌ | ❌ |
| aircraft_premium | ✅ | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ |
| ground_control | ✅ | ✅ | ❌ | ✅ | ❌ | ❌ | ❌ |
| maintenance | ✅ | ❌ | ✅ | ✅ | ✅ | ❌ | ❌ |
| admin | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |

## Usage

### Requesting a Token with Role

```bash
# Default role (aircraft_standard)
curl -X POST http://localhost:8000/auth/token \
  -H "Content-Type: application/json" \
  -d '{"aircraft_id": "550e8400-e29b-41d4-a716-446655440000"}'

# With specific role
curl -X POST http://localhost:8000/auth/token \
  -H "Content-Type: application/json" \
  -d '{
    "aircraft_id": "550e8400-e29b-41d4-a716-446655440000",
    "role": "aircraft_premium"
  }'
```

### JWT Token Structure

The JWT token includes the role in its claims:

```json
{
  "sub": "550e8400-e29b-41d4-a716-446655440000",
  "aud": "skylink",
  "iat": 1703174400,
  "exp": 1703175300,
  "role": "aircraft_premium"
}
```

### Error Responses

#### 401 Unauthorized
Missing or invalid JWT token.

```json
{
  "detail": "Missing Authorization header"
}
```

#### 403 Forbidden
Valid token but insufficient permissions.

```json
{
  "detail": "Permission denied: contacts:read required"
}
```

## Implementation

### Adding RBAC to an Endpoint

```python
from fastapi import APIRouter, Depends
from skylink.rbac import require_permission
from skylink.rbac_roles import Permission

router = APIRouter()

@router.get("/protected")
async def protected_endpoint(
    token: dict = Depends(require_permission(Permission.WEATHER_READ))
):
    # Token is verified and has required permission
    return {"message": "Access granted"}
```

### Checking Multiple Permissions

```python
@router.get("/multi-protected")
async def multi_protected(
    token: dict = Depends(require_permission(
        Permission.TELEMETRY_READ,
        Permission.CONFIG_READ
    ))
):
    # Requires BOTH permissions
    return {"message": "Access granted"}
```

### Role-Based Restriction

```python
from skylink.rbac import require_role
from skylink.rbac_roles import Role

@router.get("/admin-only")
async def admin_only(
    token: dict = Depends(require_role(Role.ADMIN))
):
    # Only admin role allowed
    return {"message": "Admin access granted"}
```

## Audit Logging

All authorization decisions are logged for security audit:

### Authorization Success
```json
{
  "event_type": "AUTHZ_SUCCESS",
  "event_category": "authorization",
  "severity": "info",
  "actor": {"type": "aircraft", "id": "550e8400..."},
  "details": {
    "role": "aircraft_premium",
    "permission": "contacts:read",
    "endpoint": "/contacts/"
  }
}
```

### Authorization Failure
```json
{
  "event_type": "AUTHZ_FAILURE",
  "event_category": "authorization",
  "severity": "warning",
  "actor": {"type": "aircraft", "id": "550e8400..."},
  "details": {
    "role": "aircraft_standard",
    "required_permission": "contacts:read",
    "endpoint": "/contacts/"
  }
}
```

## Security Considerations

1. **Default Role**: Tokens without explicit role default to `aircraft_standard` (least privilege).

2. **Invalid Roles**: Invalid role values in tokens fall back to `aircraft_standard`.

3. **Role in Token**: Role is embedded in JWT, preventing runtime escalation.

4. **No Role Enumeration**: 403 responses don't reveal which roles would have access.

5. **Authentication vs Authorization**:
   - 401 = Authentication failure (no/invalid token)
   - 403 = Authorization failure (valid token, wrong permissions)

## Future Enhancements

- Role hierarchy (inheritance)
- Dynamic role assignment from database
- Time-based permissions
- Scope-based access (e.g., specific aircraft only)
