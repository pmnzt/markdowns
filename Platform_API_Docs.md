# Platform API Docs

Platform API documentation Version 1

## Table of Contents
- [Create a new customer account](#create-a-new-customer-account)
- [Verify account](#verify-account)
- [Login](#login)
- [Request login OTP code](#request-login-otp-code)
- [Login with OTP](#login-with-otp)
- [Forgot password](#forgot-password)
- [Verify reset password code](#verify-reset-password-code)
- [Reset user password](#reset-user-password)
- [Change user password](#change-user-password)
- [Logout](#logout)
- [User Profile](#user-profile)
- [Permissions](#permissions)
- [Roles](#roles)
- [Create role with permissions](#create-role-with-permissions)
- [Update permissions of the role](#update-permissions-of-the-role)
- [Delete role](#delete-role)
- [Assign role to user](#assign-role-to-user)
- [Get user roles](#get-user-roles)
- [Delete a user's role](#delete-a-users-role)
- [Get user permissions](#get-user-permissions)
- [Get users list](#get-users-list)
- [Get admins and staff list](#get-admins-and-staff-list)
- [Get tenant defaults configurations](#get-tenant-defaults-configurations)
- [Update tenant defaults configurations](#update-tenant-defaults-configurations)
- [Get tenant configurations](#get-tenant-configurations)
- [Update tenant configurations](#update-tenant-configurations)
- [Get Tenant Frontend Initialization](#Tenant-Frontend-Initialization)
 

## Endpoints
**Base API URL: http://omalh.dev/api/v1**

**Header body for all api requests**
| Key | Value | Required |
|----------|----------|----------|
| Language    | AR \ EN | No |
| X-Omalh-Unique-Key    | api key | Yes |

<br/>


### Create a new customer account.
**POST:** /auth/register

**Request Body (POST Data):**
| Key | Value | Required |
|----------|----------|----------|
| name    | Customer name | Yes |
| login_id    | `email`  `mobile` | Yes |
| login_method | `mobile` `username` `email` | Yes |
| status    | `active` `unconfirmed` `blocked` | No (default: `unconfirmed`)|
| password    | The password should be a minimum of 6 characters and a maximum of 32 characters. | Yes |
| password_confirmation |  | Yes |
| email |  | No |
| mobile |  | No |
| avatar_url |  | No |
| company |  | No |
| address |  | No |
| city |  | No |
| state |  | No |
| country |  | Yes |

**Success Response (When status set as active):**
```
{
    "status": 200,
    "msg": "User created successfully",
    "server_time": "2023-11-14T04:16:47.698127Z",
    "data": {
        "access_token": "..."
    },
    "meta": []
}
```
**Success Response (When status set as unconfirmed):**
```
{
    "status": 200,
    "msg": "Registration successful. Please enter the verification code sent to your email.",
    "server_time": "2023-11-14T04:21:26.066342Z",
    "data": [],
    "meta": []
}
```
**Error Response**
```
{
    "status": 422,
    "msg": "Please provide valid data for the required fields.",
    "server_time": "2023-11-14T04:14:29.241166Z",
    "data": [],
    "meta": [],
    "errors": {
        "country": [
            "The country field is required."
        ],
        "password": [
            "The password field confirmation does not match."
        ]
    }
}
```


### Verify account.
**GET:** /auth/verify/{verify_code}

**Success Response:**
```
{
    "status": 200,
    "msg": "User verified successfully",
    "server_time": "2023-11-14T04:40:26.028443Z",
    "data": [],
    "meta": []
}
```

**Error Response**
```
{
    "status": 404,
    "msg": "Verification token not found",
    "server_time": "2023-11-14T04:37:53.967580Z",
    "data": [],
    "meta": []
}
```


### Login.
**POST:** /auth/login

**Request Body(Query Parameters):**
| Key | Value | Required |
|----------|----------|----------|
| includes    | `profile` | No |

**Request Body(POST Data):**
| Key | Value | Required | Description |
|----------|----------|----------|----------|
| login_id    | email / mobile | Yes |
| password    | account password | Yes |
| force_send_verify_code | `false(0)`  `true(1)` | No | Sometimes the verification code may not be delivered to the user for various reasons. By default, if the verification code is not expired, a new one will not be generated. However, you can set the value of this parameter to true to resend the verification code again. This allows you to retrigger the delivery of the verification code even if the previous one is still valid.


**Success Response:**
```
{
    "status": 200,
    "msg": "Congratulations! You have successfully logged in.",
    "server_time": "2023-11-14T04:31:18.850799Z",
    "data": {
        "token": "...",
        "token_type": "Bearer",
        "expire_in": 31622400
    },
    "meta": []
}
```
**Success Response (When account need verification):**
```
{
    "status": 202,
    "msg": "Your account is not yet confirmed. Please enter the verification code sent to your email.",
    "server_time": "2023-11-14T04:27:51.258035Z",
    "data": [],
    "meta": []
}
```
**Success Response (When include profile):**
```
{
    "status": 200,
    "msg": "Congratulations! You have successfully logged in.",
    "server_time": "2023-11-14T05:49:29.177067Z",
    "data": {
        "token": "...",
        "token_type": "Bearer",
        "expire_in": 31622400
    },
    "meta": null,
    "includes": {
        "profile": {
            "user_id": 1,
            "name": "Omar",
            "login_method": "email",
            "login_id": "omar@example.com",
            "email": null,
            "mobile": '9115577889',
            "status": "active",
            "avatar_url": null,
            "company": null,
            "address": null,
            "location": {
                "city": null,
                "state": null,
                "country": "Yemen"
            },
            "is_verified": false,
            "verified_at": null,
            "created_at": "2023-11-14T04:31:07.000000Z",
            "updated_at": "2023-11-14T04:31:07.000000Z"
        }
    }
}
```
**Error Response**
```

// Error 1
{
    "status": 422,
    "msg": "Please provide valid data for the required fields.",
    "server_time": "2023-11-14T04:29:39.257713Z",
    "data": [],
    "meta": [],
    "errors": {
        "password": [
            "The password field is required."
        ]
    }
}

// Error 2
{
    "status": 401,
    "msg": "The provided password is incorrect.",
    "server_time": "2023-11-14T04:30:22.385528Z",
    "data": [],
    "meta": []
}
```


### Login With OTP.
**POST:** /auth/login/otp

**Request Body(Query Parameters):**
| Key | Value | Required |
|----------|----------|----------|
| includes    | `profile` | No |

**Request Body(POST Data):**
| Key | Value | Required | Description |
|----------|----------|----------|----------|
| otp_code    | The OTP code that was sent to the user. | Yes |

**Success Response:**
```
{
    "status": 200,
    "msg": "Congratulations! You have successfully logged in.",
    "server_time": "2023-11-14T04:31:18.850799Z",
    "data": {
        "token": "...",
        "token_type": "Bearer",
        "expire_in": 31622400
    },
    "meta": []
}
```
**Error Response**
```
{
    "status": 404,
    "msg": "The OTP code has expired or does not exist.",
    "server_time": "2023-11-15T14:11:03.982060Z",
    "data": [],
    "meta": []
}
```


### Request Login OTP Code
**POST:** /auth/otp/create

**Request Body(POST Data):**
| Key | Value | Required | Description |
|----------|----------|----------|----------|
| login_id    | email / mobile | Yes |

**Success Response:**
```
{
    "status": 200,
    "msg": "OTP code sent successfully to your email.",
    "server_time": "2023-11-15T14:30:22.823719Z",
    "data": [],
    "meta": []
}
```


### Forgot password
**POST:** /auth/forgot-password

**Request Body(POST Data):**
| Key | Value | Required | Description |
|----------|----------|----------|----------|
| login_id    | email / mobile | Yes |

**Success Response:**
```
{
    "status": 200,
    "msg": "Reset password code was sent successfully to your email",
    "server_time": "2023-12-03T22:04:19.662278Z",
    "data": [],
    "meta": []
}
```


### Verify reset password code.
check if the reset password code is valid

**GET:** /auth/reset-password/check/{reset_code}

**Success Response:**
```
{
    "status": 200,
    "msg": "Reset password code is valid",
    "server_time": "2023-12-03T22:55:57.479499Z",
    "data": [],
    "meta": []
}
```

**Error Response**
```
{
    "status": 404,
    "msg": "Reset password code has expired.",
    "server_time": "2023-12-03T22:04:37.683809Z",
    "data": [],
    "meta": []
}
```


### Reset user password
**POST:** /auth/reset-password

**Request Body(POST Data):**
| Key | Value | Required | Description |
|----------|----------|----------|----------|
| reset_code    |  | Yes | The received code for resetting the password.
| new_password    |  | Yes | The new user password

**Success Response:**
```
{
    "status": 200,
    "msg": "Your password has been reset.",
    "server_time": "2023-12-03T21:59:25.399233Z",
    "data": [],
    "meta": []
}
```


### Change user password
**POST:** /auth/change-password

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization    | Bearer token | Yes |

**Request Body(POST Data):**
| Key | Value | Required | Description |
|----------|----------|----------|----------|
| current_password    |  | Yes | Current user password
| new_password    |  | Yes | The new user password

**Success Response:**
```
{
    "status": 200,
    "msg": "Password changed successfully",
    "server_time": "2023-12-03T23:42:36.100508Z",
    "data": [],
    "meta": []
}
```


### Logout.
**GET:** /auth/logout

logs out the user associated with the token by invalidating it. Once revoked, the user will no longer have access to protected resources or be able to perform authenticated actions until a new valid token is obtained.

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization    | Bearer token | Yes |

**Success Response:**
```
{
    "status": 200,
    "msg": "You have been successfully logged out!",
    "server_time": "2023-11-14T04:44:45.042855Z",
    "data": [],
    "meta": []
}
```


### User Profile.
**GET:** /users/profile

Get authenticated user profile.

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization    | Bearer token | Yes |

**Success Response:**
```
{
    "status": 200,
    "msg": "Ok",
    "server_time": "2023-11-16T02:45:40.882472Z",
    "data": {
        "user_id": 1,
        "name": "Omar",
        "login_method": "mobile",
        "login_id": "3322115544",
        "email": null,
        "mobile": null,
        "status": "active",
        "avatar_url": null,
        "company": null,
        "address": null,
        "location": {
            "city": null,
            "state": null,
            "country": "Yemen"
        },
        "is_verified": true,
        "verified_at": "2023-11-15T14:26:42.000000Z",
        "created_at": "2023-11-13T11:18:20.000000Z",
        "updated_at": "2023-11-15T14:26:42.000000Z"
    },
    "meta": []
}
```

### Permissions
**GET:** /permissions

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization    | Bearer token | Yes |

**Success Response:**
```
{
    "status": 200,
    "msg": "Ok",
    "server_time": "2023-11-16T11:27:43.864693Z",
    "data": [
        {
            "name": "show-admins-list",
            "id": 5
        },
        {
            "name": "create-admin",
            "id": 8
        },
        {
            "name": "create-customer",
            "id": 4
        },
        {
            "name": "show-customers-list",
            "id": 1
        }
    ],
    "meta": []
}
```

### Create role with permissions
**POST:** /roles


**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization    | Bearer token | Yes |


**Request Body(POST Data):**
| Key | Value | Required |
|----------|----------|----------|
| role_name    | any name for the role | Yes
| permissions[]    | permission name (Permission should be exists in [Permissions](#Permissions)) | Yes

**Success Response:**
```
{
    "status": 201,
    "msg": "Role and permissions created successfully",
    "server_time": "2023-11-16T12:21:42.009196Z",
    "data": [],
    "meta": []
}
```

### Roles
**GET:** /roles

Show all roles

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization    | Bearer token | Yes |

**Request Body(Query Parameters):**
| Key | Value | Required |
|----------|----------|----------|
| includes    | `permissions` | No |

**Success Response:**
```
{
    "status": 200,
    "msg": "Ok",
    "server_time": "2023-11-16T18:08:52.018526Z",
    "data": [
        {
            "id": 1,
            "name": "admin"
        },
        {
            "id": 2,
            "name": "staff"
        },
        {
            "id": 3,
            "name": "accountant"
        }
    ],
    "meta": []
}
```

**Success Response (When includes permissions):**
```
{
    "status": 200,
    "msg": "Ok",
    "server_time": "2023-11-16T12:19:22.841870Z",
    "data": [
        {
            "id": 1,
            "name": "admin",
            "permissions": [
                {
                    "id": 2,
                    "name": "edit-customer",
                    "pivot": {
                        "role_id": 1,
                        "permission_id": 2
                    }
                },
                {
                    "id": 3,
                    "name": "delete-customer",
                    "pivot": {
                        "role_id": 1,
                        "permission_id": 3
                    }
                },
                {
                    "id": 4,
                    "name": "create-customer",
                    "pivot": {
                        "role_id": 1,
                        "permission_id": 4
                    }
                }
            ]
        }
    ],
    "meta": []
}
```
### Update permissions of the role
**PUT:** /roles/{role_id}

This action will sync role permissions, which means you have to set all role permissions in this request. If any permissions are not included in the request, the existing permissions will be removed, and only the new ones provided will be used.

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization    | Bearer token | Yes |


**Request Body(PUT Data):**
| Key | Value | Required | Description |
|----------|----------|----------|----------|
| permissions[]    | permission name (Permission should be exists in [Permissions](#Permissions)) | Yes | Array of updated permissions

**Success Response:**
```
{
    "status": 200,
    "msg": "Role updated successfully",
    "server_time": "2023-11-16T19:01:02.962389Z",
    "data": [],
    "meta": []
}
```

### Delete role
**DELETE:** /roles/{role_id}


**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization    | Bearer token | Yes |

**Success Response:**
```
{
    "status": 200,
    "msg": "Role deleted successfully",
    "server_time": "2023-11-16T19:01:02.962389Z",
    "data": [],
    "meta": []
}
```

### Assign role to user
**POST:** /users/{user_id}/roles

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization | Bearer token | Yes |

**Request Body(POST Data):**
| Key | Value | Required |
|----------|----------|----------|
| role_name | Role name should be exists in [Roles](#Roles) | Yes


**Success Response:**
```
{
    "status": 200,
    "msg": "Role assigned successfully",
    "server_time": "2023-11-18T14:17:56.904660Z",
    "data": [],
    "meta": []
}
```

### Get user roles
**GET:** /users/{user_id}/roles

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization    | Bearer token | Yes |

**Success Response:**
```
{
    "status": 200,
    "msg": "Ok",
    "server_time": "2023-11-18T13:53:54.835359Z",
    "data": [
        "admin"
    ],
    "meta": []
}
```

### Delete a user's role
**DELETE:** /users/{user_id}/roles/{role_name}

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization    | Bearer token | Yes |

**Success Response:**
```
{
    "status": 200,
    "msg": "Role removed from the user successfully",
    "server_time": "2023-11-18T14:45:47.725001Z",
    "data": [],
    "meta": []
}
```

### Get user permissions
**GET:** /users/{user_id}/permissions

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization | Bearer token | Yes |

**Success Response:**
```
{
    "status": 200,
    "msg": "Ok",
    "server_time": "2023-11-18T16:33:47.932647Z",
    "data": [
        {
            "name": "edit-customer",
            "pivot": {
                "role_id": 4,
                "permission_id": 2
            }
        },
        {
            "name": "delete-customer",
            "pivot": {
                "role_id": 4,
                "permission_id": 3
            }
        }
    ],
    "meta": []
}
```

### Get users list
**GET:** /users

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization | Bearer token | Yes |

**Request Body(GET Data):**
| Key | Value | Required | Description |
|----------|----------|----------|----------|
| sort_column | `user_id` `country` `login_method` `name` `email` `mobile` `status` | No | Default: `user_id`
| sort_order | `desc` `asc` | No | Default: `desc`
| page | | No |
| per_page | | No | default: `20`, Maximum number of items allowed per page is `100`
| name | | No |
| login_method | `email`  `mobile` | No |
| email | | No |
| mobile | | No |
| status | | No |
| country | | No |


**Success Response:**
```
{
    "status": 200,
    "msg": "Ok",
    "server_time": "2023-11-22T05:25:01.741754Z",
    "data": [
        {
            "user_id": 1,
            "name": "Admin",
            "login_method": "email",
            "login_id": "admin@omalh.com",
            "email": null,
            "mobile": null,
            "status": "active",
            "avatar_url": null,
            "company": null,
            "address": null,
            "location": {
                "city": null,
                "state": null,
                "country": null
            },
            "is_verified": true,
            "verified_at": "2023-11-16T04:07:08.000000Z",
            "created_at": "2023-11-16T04:07:08.000000Z",
            "updated_at": "2023-11-16T04:07:08.000000Z"
        },
        {
            "user_id": 63,
            "name": "Yousif",
            "login_method": "email",
            "login_id": "yousif@example.com",
            "email": null,
            "mobile": null,
            "status": "active",
            "avatar_url": null,
            "company": null,
            "address": null,
            "location": {
                "city": null,
                "state": null,
                "country": "oman"
            },
            "is_verified": false,
            "verified_at": null,
            "created_at": "2023-11-14T04:31:07.000000Z",
            "updated_at": "2023-11-14T04:31:07.000000Z"
        }
    ],
    "meta": {
        "pagination": {
            "total_items": 55,
            "per_page": 20,
            "current_page": 1,
            "last_page": 3
        },
        "sort_column": "user_id",
        "sort_order": "desc"
    }
}
```

### Get admins and staff list
**GET:** /users/admins

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization | Bearer token | Yes |

**Request Body(GET Data):**
| Key | Value | Required | Description |
|----------|----------|----------|----------|
| sort_column | `user_id` `country` `login_method` `name` `email` `mobile` `status` | No | Default: `user_id`
| sort_order | `desc` `asc` | No |
| page | | No |
| per_page | | No | default: `20`, Maximum number of items allowed per page is `100`
| name | | No |
| login_method | `email`  `mobile` | No |
| email | | No |
| mobile | | No |
| status | | No |
| country | | No |


**Success Response:**
```
{
    "status": 200,
    "msg": "Ok",
    "server_time": "2023-11-22T05:48:08.977863Z",
    "data": [
        {
            "user_id": 1,
            "name": "Admin",
            "login_method": "email",
            "login_id": "admin@omalh.com",
            "email": null,
            "mobile": null,
            "status": "active",
            "avatar_url": null,
            "company": null,
            "address": null,
            "location": {
                "city": null,
                "state": null,
                "country": null
            },
            "is_verified": true,
            "verified_at": "2023-11-16T04:07:08.000000Z",
            "created_at": "2023-11-16T04:07:08.000000Z",
            "updated_at": "2023-11-16T04:07:08.000000Z"
        }
    ],
    "meta": {
        "pagination": {
            "total_items": 1,
            "per_page": 20,
            "current_page": 1,
            "last_page": 1
        },
        "sort_column": "user_id",
        "sort_order": "desc"
    }
}
```




### Get tenant defaults configurations
**GET:** /tenants/config/defaults

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization | Bearer token | Yes |

**Success Response:**
```
{
    "status": 200,
    "msg": "Ok",
    "server_time": "2023-12-15T05:10:16.669785Z",
    "data": [
        {
            "group_name": "resources",
            "config_name": "max_storage",
            "config_value": "1024",
            "value_type": "integer"
        },
        {
            "group_name": "features",
            "config_name": "custom_email_config",
            "config_value": "1",
            "value_type": "boolean"
        },
        {
            "group_name": "features",
            "config_name": "otp_login",
            "config_value": "1",
            "value_type": "boolean"
        },
        {
            "group_name": "features",
            "config_name": "sms_service",
            "config_value": "0",
            "value_type": "boolean"
        }
    ],
    "meta": []
}
```


### Update tenant defaults configurations
**PUT:** /tenants/config/defaults/{configName}

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization | Bearer token | Yes |

**Request Body(PUT Data):**
| Key | Value | Required |
|----------|----------|----------|
| config_value | the new value | Yes |

**Success Response:**
```
{
    "status": 200,
    "msg": "تم التحديث بنجاح",
    "server_time": "2023-12-18T01:33:19.345059Z",
    "data": [],
    "meta": []
}
```

**Error Response:**
```
{
    "status": 404,
    "msg": "لم يتم العثور على configuration",
    "server_time": "2023-12-18T01:34:17.210873Z",
    "data": [],
    "meta": []
}
```


### Get tenant configurations
**GET:** /tenants/{tenant_id}/config

**Success Response:**
```
{
    "status": 200,
    "msg": "Ok",
    "server_time": "2023-12-19T01:20:21.073453Z",
    "data": [
        {
            "group_name": "features",
            "config_name": "custom_email_config",
            "config_value": "1",
            "value_type": "boolean",
            "default": true
        },
        {
            "group_name": "features",
            "config_name": "otp_login",
            "config_value": "1",
            "value_type": "boolean",
            "default": true
        },
        {
            "group_name": "features",
            "config_name": "sms_service",
            "config_value": "0",
            "value_type": "boolean",
            "default": true
        },
        {
            "group_name": null,
            "config_name": "max_storage",
            "config_value": "2048",
            "value_type": "integer",
            "default": false,
            "default_config_value": "1024"
        }
    ],
    "meta": []
}
```

### Update tenant configurations
**POST:** /tenants/{tenant_id}/config

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization | Bearer token | Yes |

**Request Body(POST Data):**
| Key | Value | Required |
|----------|----------|----------|
| config_name | Configuration name should be exists in [Tenant defaults configurations](#Get-tenant-defaults-configurations) | Yes
| config_value | The custom configuration value for tenant | Yes

**Success Response:**
```
{
    "status": 200,
    "msg": "تم تحديث الإعدادات بنجاح",
    "server_time": "2023-12-19T21:19:20.542363Z",
    "data": [],
    "meta": []
}
```

**Error Response:**
```
{
    "status": 404,
    "msg": "لم يتم العثور على المتجر.",
    "server_time": "2023-12-19T21:21:47.350854Z",
    "data": [],
    "meta": []
}
```
**Error Response:**
```
{
    "status": 422,
    "msg": "يرجى ادخال بيانات صالحة للحقول المطلوبة.",
    "server_time": "2023-12-19T21:27:43.339278Z",
    "data": [],
    "meta": [],
    "errors": {
        "config_value": [
            "Invalid data type, required boolean type"
        ]
    }
}
```




### Tenant Frontend Initialization
**GET:** /tenants/frontend/init

**Request Body(Query Parameters):**
| Key | Value | Required | Description |
|----------|----------|----------|----------|
| domain    | tenant domain\subdomain | Yes | Set the website domain, and it will return init data for the tenant |

**Success Response:**
```
{
    "status": 200,
    "msg": "Ok",
    "server_time": "2023-12-31T22:11:08.299386Z",
    "data": {
        "tenant_id": "tenant3",
        "api_domain": "tenant3.omalh-local.dev"
    },
    "meta": []
}
```
**Error Response:**
```
{
    "status": 404,
    "msg": "لم يتم العثور على المتجر.",
    "server_time": "2023-12-31T22:12:22.755286Z",
    "data": [],
    "meta": []
}
```
