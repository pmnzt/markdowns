# Tenant API Docs

Tenant API documentation Version 1

## Table of Contents

- [Auth](#auth)
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
- [User & Roles & Permissions](#users--roles--permissions)
  - [User Profile](#user-profile)
  - [Update user Profile](#update-user-profile)
  - [Permissions](#permissions)
  - [Roles](#roles)
  - [Create role with permissions](#create-role-with-permissions)
  - [Update permissions of the role](#update-permissions-of-the-role)
  - [Delete role](#delete-role)
  - [Assign role to user](#assign-role-to-user)
  - [Get user roles](#get-user-roles)
  - [Check if a user has a specific role](#Check-if-a-user-has-a-specific-role)
  - [Check if a user has a specific permission](#Check-if-a-user-has-a-specific-permission)
  - [Delete a user's role](#delete-a-users-role) 
  - [Get user permissions](#get-user-permissions)
  - [Get users list](#get-users-list)
  - [Get admins and staff list](#get-admins-and-staff-list)
- [Frontend](#frontend)
  - [Get navbar items](#get-frontend-website-navbar)
  - [Get navbar item by id](#Get-frontend-website-navbar-item-by-id)
  - [Create a new navbar item](#create-a-new-frontend-website-navbar-item)
  - [Delete navbar item by id](#delete-fronted-website-navbar-item-by-id)
  - [Update navbar item](#Update-fronted-website-navbar-item)
- [Settings](#Settings)
  - [Setup custom email configuration](#Setup-custom-email-configuration)
  - [Get custom email configuration](#get-custom-email-configuration)
  - [Delete custom email configuration](#delete-custom-email-configuration)
- [Product categories](#Product-Categories)
    - [Create category](#Create-category)
    - [Get main categories](#Get-main-categories)
    - [Get category by id](#Get-category-by-id)
    - [Get subcategories of a category](#Get-subcategories-of-a-category)
    - [Get all categories](#Get-all-categories)
    - [Move category to trash](#Move-category-to-trash)
    - [Restore category from trash](#Restore-category-from-trash)
    - [Delete category completely from database](#Delete-category-completely-from-database)
    - [Get deleted categories](#Get-deleted-categories)



## Endpoints

**Base API URL: omalh.dev/tenants/{tenant_id}/api/v1**

**Header body for all API requests**
| Key | Value | Required |
|----------|----------|----------|
| Language | `AR` `EN` | No |
| X-Omalh-Unique-Key | api key | Yes |

<br/>

## Auth

### Create a new customer account.

**POST:** /auth/register

**Request Body (POST Data):**
| Key | Value | Required |
|----------|----------|----------|
| name | Customer name | Yes |
| login_id | email / mobile | Yes |
| login_method | `mobile` `username` `email` | Yes |
| status | `active` `unconfirmed` `blocked` | No (default: unconfirmed)|
| password | The password should be a minimum of 6 characters and a maximum of 32 characters. | Yes |
| password_confirmation | | Yes |
| email | | No |
| mobile | | No |
| avatar_url | | No |
| company | | No |
| address | | No |
| city | | No |
| state | | No |
| country | | Yes |

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

### Login.

**POST:** /auth/login

**Request Body(Query Parameters):**
| Key | Value | Required |
|----------|----------|----------|
| includes | `profile` `roles` | No |
| adminLogin |  | No |


**Request Body(POST Data):**
| Key | Value | Required | Description |
|----------|----------|----------|----------|
| login_id | email / mobile | Yes |
| password | account password | Yes |
| force_send_verify_code | false(0) / true(1) | No | Sometimes the verification code may not be delivered to the user
for various reasons. By default, if the verification code is not expired, a new one will not be generated. However, you
can set the value of this parameter to true to resend the verification code again. This allows you to retrigger the
delivery of the verification code even if the previous one is still valid.

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

**Success Response (When include profile & roles):**

```
{
    "status": 200,
    "msg": "تهانينا! لقد قمت بتسجيل الدخول بنجاح",
    "server_time": "2024-01-06T04:52:21.830414Z",
    "data": {
        "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJhdWQiOiIxMCIsImp0aSI6ImI3NDdlNWIzZGNiN2M2YTg0MmNjMWUzZWMxZmQzY2ZjMmY1ZTRmYzljYTQ3NDdlM2QwNjdlYmQwOGRhNTAzMGI1N2ZlNzM2ZDgyZjEyMjY0IiwiaWF0IjoxNzA0NTE2NzQxLjgwMzU2NywibmJmIjoxNzA0NTE2NzQxLjgwMzU3MSwiZXhwIjoxNzM2MTM5MTQxLjc5MTE5OSwic3ViIjoiMSIsInNjb3BlcyI6W119.KKkHlWqV9p73qNlPYXzzttpy6km9nHnZwHLDrP9PPE82z0ntKCwxAW2y17MV0mzgSPlCo_DrIim_SS8W7ZmI76DuHLrEKmuKgDjrmY7C4XK_qD6KAVWqtn2BL-yx7PDQ27JheE_PZDzjiHpkaGvO4Zkk5MvQu-6sORn8JvaJ3MT1dtneCaP-ED7oIkWeCuDeXDeIHoRdDEW_Leo4V-F55yToQ2f6FIYYDeJyQILqlKgLg7rtjmesgkw1uXCRn-o7FHDo6AI5udNHbkqNZwhL5PqcpkkjWDFCt7njmblhsVWF2e9cwpdzKfjwjYwz14rI-Ic5rUb41tvzZylKoJxVK10C8lkOOBt12G1HY0CfDDjs_v4hHI4HibbSqpjaCM79I8vMJ-5tZ2ydNsjIcskMTO32QtN6RgmI7Hw62jCGOKTqSvPdue-HxlxfO7_O3CnEigMtkIewTs84YSkmf6chj9ttHsUdqwnYLkCKqLvPMdiXlta7m2FhtFnN5VZ6J_fFm7M_Fey7cyqKNXaU3Ice5yaj3HocgX1LqYOvqnbQMgpzD3IeYWykzj7OHzEX5A_Pl-g7ZhBFsLBkNY17MxjkmwhsHrVNHhPiE7y1Br9neQ1T4gWSVRWCNQuqnl86C03VF6hLp4LSemmI_iGi1P2XfxWQEIsQubc2gtpvvmqqht4",
        "token_type": "Bearer",
        "expire_in": 2591999
    },
    "meta": null,
    "includes": {
        "profile": {
            "user_id": 1,
            "name": "Ahmed",
            "login_method": "email",
            "login_id": "customer@example.com",
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
            "verified_at": "2023-11-28T19:51:05.000000Z",
            "created_at": "2023-11-28T19:51:05.000000Z",
            "updated_at": "2023-12-04T21:53:07.000000Z"
        },
        "roles": [
            {
                "id": 1,
                "name": "admin"
            }
        ]
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
| includes | `profile` `roles` | No |
| adminLogin |  | No |


**Request Body(POST Data):**
| Key | Value | Required | Description |
|----------|----------|----------|----------|
| otp_code | The OTP code that was sent to the user. | Yes |

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
| login_id | email / mobile | Yes |

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

### Forgot password

**POST:** /auth/forgot-password

**Request Body(POST Data):**
| Key | Value | Required | Description |
|----------|----------|----------|----------|
| login_id | email / mobile | Yes |

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
| reset_code | | Yes | The received code for resetting the password.
| new_password | | Yes | The new user password

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
| Authorization | Bearer token | Yes |

**Request Body(POST Data):**
| Key | Value | Required | Description |
|----------|----------|----------|----------|
| current_password | | Yes | Current user password
| new_password | | Yes | The new user password

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

logs out the user associated with the token by invalidating it. Once revoked, the user will no longer have access to
protected resources or be able to perform authenticated actions until a new valid token is obtained.

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization | Bearer token | Yes |

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

## Users , Roles , Permissions

### User Profile.

**GET:** /users/profile

Get authenticated user profile.

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization | Bearer token | Yes |

**Success Response:**

```
{
    "status": 200,
    "msg": "Ok",
    "server_time": "2023-11-16T02:45:40.882472Z",
    "data": {
        "user_id": 57,
        "name": "Ahmed",
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
            "country": "oman"
        },
        "is_verified": true,
        "verified_at": "2023-11-15T14:26:42.000000Z",
        "created_at": "2023-11-13T11:18:20.000000Z",
        "updated_at": "2023-11-15T14:26:42.000000Z"
    },
    "meta": []
}
```


### Update user Profile.

**PUT:** /users/profile

Update authenticated user profile.

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization | Bearer token | Yes |


**Request Body(PUT Data):**
| Key | Value | Required |
|----------|----------|----------|
| name | | Yes |
| email | | No |
| mobile | | No |
| avatar_url | | No |
| company | | No |
| address | | No |
| city | | No |
| state | | No |
| country | | Yes |


**Success Response:**

```
{
    "status": 200,
    "msg": "User profile updated successfully",
    "server_time": "2024-02-17T18:57:06.386527Z",
    "data": [],
    "meta": []
}
```


### Permissions

**GET:** /permissions

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization | Bearer token | Yes |

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
| Authorization | Bearer token | Yes |

**Request Body(POST Data):**
| Key | Value | Required |
|----------|----------|----------|
| role_name | any name for the role | Yes
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
| Authorization | Bearer token | Yes |

**Request Body(Query Parameters):**
| Key | Value | Required |
|----------|----------|----------|
| includes | `permissions` | No |

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

This action will sync role permissions, which means you have to set all role permissions in this request. If any
permissions are not included in the request, the existing permissions will be removed, and only the new ones provided
will be used.

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization | Bearer token | Yes |

**Request Body(PUT Data):**
| Key | Value | Required | Description |
|----------|----------|----------|----------|
| permissions[]    | permission name (Permission should be exists in [Permissions](#Permissions)) | Yes | Array of
updated permissions

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
| Authorization | Bearer token | Yes |

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
| Authorization | Bearer token | Yes |

**Request Body(Query Parameters):**
| Key | Value | Required |
|----------|----------|----------|
| includes | `permissions` | No |

**Path Parameters:**
| Key | Description |
|----------|----------|
| user_id | The ID of the user or `@me` to retrieve roles for the current authenticated user. |


**Success Response:**

```
{
    "status": 200,
    "msg": "Ok",
    "server_time": "2023-11-18T13:53:54.835359Z",
    "data": [
       {
            "id": 1,
            "name": "admin"
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
    "server_time": "2024-01-06T04:45:16.238871Z",
    "data": [
        {
            "id": 1,
            "name": "admin"
        }
    ],
    "meta": [],
    "includes": {
        "permissions": [
            {
                "id": 1,
                "name": "show-customers-list"
            },
            {
                "id": 2,
                "name": "edit-customer"
            },
            {
                "id": 3,
                "name": "delete-customer"
            }
        ]
    }
}
```


### Delete a user's role

**DELETE:** /users/{user_id}/roles/{role_name}

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization | Bearer token | Yes |

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

### Check if a user has a specific role

**GET:** /users/{user_id}/has-role/{role_name}

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization | Bearer token | Yes |

**Success Response:**

```
{
    "status": 200,
    "msg": "Ok",
    "server_time": "2024-01-06T06:25:05.346801Z",
    "data": {
        "has_role": true
    },
    "meta": []
}
```

### Get user permissions

**GET:** /users/{user_id}/permissions

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization | Bearer token | Yes |

**Path Parameters:**
| Key | Description |
|----------|----------|
| user_id | The ID of the user or `@me` to retrieve permissions for the current authenticated user. |


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


### Check if a user has a specific permission

**GET:** /users/{user_id}/has-permission/{role_permission}

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization | Bearer token | Yes |

**Success Response:**

```
{
    "status": 200,
    "msg": "Ok",
    "server_time": "2024-01-06T06:25:05.346801Z",
    "data": {
        "has_permission": false
    },
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
| sort_column | `country` `login_method` `name` `email` `mobile` `status` | No |
| sort_order | `desc` `asc` | No |
| page | | No |
| per_page | | No | default: 20, Maximum number of items allowed per page is 100
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
            "user_id": 64,
            "name": "Admin",
            "login_method": "email",
            "login_id": "admin@tenant.omalh.com",
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
            "name": "Ahmed",
            "login_method": "email",
            "login_id": "853@example.com",
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
        },
        {
            "user_id": 62,
            "name": "Ahmed",
            "login_method": "email",
            "login_id": "85@example.com",
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
            "is_verified": true,
            "verified_at": "2023-11-14T04:40:26.000000Z",
            "created_at": "2023-11-14T04:21:18.000000Z",
            "updated_at": "2023-11-14T04:40:26.000000Z"
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
| sort_column | `country` `login_method` `name` `email` `mobile` `status` | No |
| sort_order | `desc` `asc` | No |
| page | | No |
| per_page | | No | default: 20, Maximum number of items allowed per page is 100
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
            "user_id": 64,
            "name": "Admin",
            "login_method": "email",
            "login_id": "admin@tenant.omalh.com",
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

## Settings

### Setup custom email configuration

**POST|PUT:** /email-config

This endpoint is used for both creating and updating. If you send a request and the email configuration does not exist, it will be created. Otherwise, it will be updated

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization | Bearer token | Yes |

**Request Body(POST Data):**
| Key | Value | Required | Description
|----------|----------|----------|----------|
| driver | `smtp` `sendmail` `mailgun` | Yes |
| host |  | Yes |
| port |  | Yes |
| username | e.g: username@example.com | Yes |
| password | | Yes |
| address | | Yes | The email address to display in email
| name | | Yes | The name to display in email
| encryption | `tls` `ssl` | Yes |

**Success Response (on create):**
```
{
    "status": 201,
    "msg": "Email configuration created successfully",
    "server_time": "2024-02-18T16:43:30.850199Z",
    "data": [],
    "meta": []
}
```

**Success Response (on update):**
```
{
    "status": 200,
    "msg": "Email configuration updated successfully",
    "server_time": "2024-02-18T16:43:30.850199Z",
    "data": [],
    "meta": []
}
```

### Get custom email configuration

**GET:** /email-config

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization | Bearer token | Yes |

**Success Response:**
```
{
    "status": 200,
    "msg": "Ok",
    "server_time": "2024-02-18T16:35:17.127468Z",
    "data": {
        "driver": "smtp",
        "host": "host.example.com",
        "port": "465",
        "username": "username@example.com",
        "password": "emailpassword",
        "address": "support@example.com",
        "name": "Support team",
        "encryption": "ssl"
    },
    "meta": []
}
```


### Delete custom email configuration

**DELETE:** /email-config

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization | Bearer token | Yes |

**Success Response:**
```
{
    "status": 200,
    "msg": "Email configuration deleted successfully",
    "server_time": "2024-02-18T16:43:27.988421Z",
    "data": [],
    "meta": []
}
```

## Frontend

### Get frontend website navbar

**GET:** /frontend/navbar-items

**Success Response:**

```
{
    "status": 200,
    "msg": "Ok",
    "server_time": "2024-02-17T07:39:05.145851Z",
    "data": [
        {
            "id": 23,
            "parent_id": null,
            "title": "Home",
            "link": "/home",
            "icon": "fas fa-home",
            "target_type": "_self",
            "sort_order": 0,
            "children": []
        },
        {
            "id": 33,
            "parent_id": null,
            "title": "Item 124",
            "link": "/Item-124",
            "icon": null,
            "target_type": "_self",
            "sort_order": 0,
            "children": [
                {
                    "id": 40,
                    "parent_id": 33,
                    "title": "Item 124",
                    "link": "/Item-124",
                    "icon": null,
                    "target_type": "_self",
                    "sort_order": 0,
                    "children": [
                        {
                            "id": 39,
                            "parent_id": 40,
                            "title": "Item 124",
                            "link": "/Item-124",
                            "icon": null,
                            "target_type": "_self",
                            "sort_order": 0,
                            "children": []
                        }
                    ]
                }
            ]
        },
        {
            "id": 40,
            "parent_id": 33,
            "title": "Item 124",
            "link": "/Item-124",
            "icon": null,
            "target_type": "_self",
            "sort_order": 0,
            "children": [
                {
                    "id": 39,
                    "parent_id": 40,
                    "title": "Item 124",
                    "link": "/Item-124",
                    "icon": null,
                    "target_type": "_self",
                    "sort_order": 0,
                    "children": []
                }
            ]
        }
    ],
    "meta": []
}
```

### Get frontend website navbar item by id

**GET:** /frontend/navbar-items/{id}

**Success Response:**

```
{
    "status": 200,
    "msg": "Ok",
    "server_time": "2024-02-17T07:16:41.869608Z",
    "data": {
            "id": 30,
            "parent_id": 27,
            "title": "Sub-menu 3",
            "link": "/sub-menu3",
            "icon": null,
            "target_type": "_blank",
            "sort_order": 0,
            "children": [
                {
                    "id": 31,
                    "parent_id": 30,
                    "title": "Item 1",
                    "link": "/item1",
                    "icon": null,
                    "target_type": "_blank",
                    "sort_order": 0,
                    "children": []
                }
            ]
        },
    "meta": []
}
```

### Create a new frontend website navbar item

**POST:** /frontend/navbar-items

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization | Bearer token | Yes |

**Request Body(POST Data):**
| Key | Value | Required | Default
|----------|----------|----------|----------|
| title | navbar item title | Yes |
| link | navbar item link | Yes |
| target_type | html `<a>` tag target, e.g: _self, _blank etc.. | No | _self
| icon | navbar item icon | No |
| parent_id | the parent navbar item id | No |
| sort_order | custom order of items | No | 0

**Success Response:**

```
{
    "status": 200,
    "msg": "Navbar item created successfully",
    "server_time": "2024-02-17T06:08:23.569133Z",
    "data": {
        "id": 1,
        "title": "Home",
        "link": "/home",
        "icon": "fas fa-home",
        "target_type": "_self",
        "sort_order": 0,
        "children": null
    },
    "meta": []
}
```

### Update fronted website navbar item

**PUT:** /frontend/navbar-items/{id}

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization | Bearer token | Yes |

**Request Body(PUT Data):**
| Key | Value | Required | Default
|----------|----------|----------|----------|
| title | navbar item title | Yes |
| link | navbar item link | Yes |
| target_type | html `<a>` tag target, e.g: _self, _blank etc.. | No | _self
| icon | navbar item icon | No |
| parent_id | the parent navbar item id | No |
| sort_order | custom order of items | No | 0

**Success Response:**

```
{
    "status": 200,
    "msg": "Navbar item updated successfully.",
    "server_time": "2024-02-17T10:07:29.849982Z",
    "data": [],
    "meta": []
}
```


### Delete fronted website navbar item by id

**DELETE:** /frontend/navbar-items/{id}

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization | Bearer token | Yes |

**Success Response:**

```
{
    "status": 200,
    "msg": "Navbar item deleted successfully.",
    "server_time": "2024-02-17T07:34:54.325583Z",
    "data": [],
    "meta": []
}
```

## Product Categories

### Create category

**POST:** /categories

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization | Bearer token | Yes |

**Request Body(POST Data):**
| Key | Value | Required | Default
|----------|----------|----------|----------|
| parent_id | parent category id | No | null
| name | | Yes | 
| slug | | No | auto generate slug from name
| description | | No | 
| image_id | | No | 
| status | `publish` `drift` | No | `publish` 


**Success Response:**

```
{
    "status": 200,
    "msg": "Ok",
    "server_time": "2024-02-21T07:43:03.591395Z",
    "data": "تم إنشاء التصنيف بنجاح",
    "meta": []
}
```


### Update category

**PUT:** /categories/{categoryId}

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization | Bearer token | Yes |

**Request Body(PUT Data):**
| Key | Value | Required | Default
|----------|----------|----------|----------|
| parent_id | parent category id | No | null
| name | | Yes |
| slug | | No | auto generate slug from name
| description | | No |
| image_id | | No |
| status | `publish` `drift` | No | `publish`


**Success Response:**

```
{
    "status": 200,
    "msg": "Ok",
    "server_time": "2024-02-23T04:23:27.273469Z",
    "data": "تم تحديث التصنيف بنجاح",
    "meta": []
}
```


### Get main categories

**GET:** /categories

**Request Body(Query Parameters):**
| Key | Value | Required | Default
|----------|----------|----------|----------|
| sort_column | `name` `category_id` `status` | No | `category_id`
| sort_order | `desc` `asc` | No | `asc`
| name | category name | No |
| status | category status | No |
| slug | category slug | No |

**Success Response:**

```
{
    "status": 200,
    "msg": "Ok",
    "server_time": "2024-02-20T17:42:50.595378Z",
    "data": [
        {
            "category_id": 1,
            "parent_id": null,
            "name": "ملابس",
            "slug": "clothes",
            "description": "Dolorem vero inventore quas illo quia rem. Ratione fugiat laboriosam et laborum veniam. Eum sint odio molestiae commodi. Accusamus molestiae sint in qui dicta.",
            "image_url": null,
            "status": "publish",
            "created_at": "2024-02-19T17:08:12.000000Z",
            "updated_at": "2024-02-19T17:30:51.000000Z",
            "subcategories": []
        },
        {
            "category_id": 10,
            "parent_id": null,
            "name": "أحذية",
            "slug": "shoes",
            "description": "Repellat voluptatum vel consequatur hic saepe qui. Quaerat inventore quibusdam molestiae adipisci. Autem fugiat non qui nobis excepturi culpa.",
            "image_url": null,
            "status": "publish",
            "created_at": "2024-02-19T17:08:12.000000Z",
            "updated_at": "2024-02-20T17:42:44.000000Z",
            "subcategories": []
        }
    ],
    "meta": []
}
```

### Get all categories

**GET:** /categories/all

**Success Response:**

```
{
    "status": 200,
    "msg": "Ok",
    "server_time": "2024-02-20T17:43:52.143665Z",
    "data": [
        {
            "category_id": 1,
            "parent_id": null,
            "name": "ملابس",
            "slug": "clothes",
            "description": "Dolorem vero inventore quas illo quia rem. Ratione fugiat laboriosam et laborum veniam. Eum sint odio molestiae commodi. Accusamus molestiae sint in qui dicta.",
            "image_url": null,
            "status": "publish",
            "created_at": "2024-02-19T17:08:12.000000Z",
            "updated_at": "2024-02-19T17:30:51.000000Z",
            "subcategories": [
                {
                    "category_id": 2,
                    "parent_id": 1,
                    "name": "رجالية",
                    "slug": "men",
                    "description": null,
                    "image_url": null,
                    "status": "drift",
                    "created_at": "2024-02-19T17:08:12.000000Z",
                    "updated_at": "2024-02-19T17:08:12.000000Z",
                    "subcategories": [
                        {
                            "category_id": 4,
                            "parent_id": 2,
                            "name": "قمصان",
                            "slug": "shirts",
                            "description": "Vero repellendus tenetur voluptas ut. Non ea incidunt deserunt rerum dolor earum. Sunt qui nihil ipsum quis totam blanditiis neque. Omnis possimus iste laborum quidem est quos.",
                            "image_url": null,
                            "status": "publish",
                            "created_at": "2024-02-19T17:08:12.000000Z",
                            "updated_at": "2024-02-19T17:08:12.000000Z",
                            "subcategories": []
                        },
                        {
                            "category_id": 5,
                            "parent_id": 2,
                            "name": "اطقم",
                            "slug": "tempora-doloremque-et-temporibus-neque-magni",
                            "description": null,
                            "image_url": null,
                            "status": "publish",
                            "created_at": "2024-02-19T17:08:12.000000Z",
                            "updated_at": "2024-02-19T17:08:12.000000Z",
                            "subcategories": []
                        }
                    ]
                },
                {
                    "category_id": 3,
                    "parent_id": 1,
                    "name": "أطفال",
                    "slug": "kids",
                    "description": "Repellendus quidem tempora ipsa tempore alias aspernatur voluptatem. Ex et eum iusto et culpa deserunt dolores. Qui ut esse libero assumenda cum. Dolor ad nam ad quia dolor.",
                    "image_url": null,
                    "status": "publish",
                    "created_at": "2024-02-19T17:08:12.000000Z",
                    "updated_at": "2024-02-19T17:08:12.000000Z",
                    "subcategories": [
                        {
                            "category_id": 6,
                            "parent_id": 3,
                            "name": "بنات",
                            "slug": "girls",
                            "description": "Beatae sequi placeat aut sed non nemo quaerat. Possimus inventore ratione id voluptas. Aliquam quisquam est libero et est hic et.",
                            "image_url": null,
                            "status": "publish",
                            "created_at": "2024-02-19T17:08:12.000000Z",
                            "updated_at": "2024-02-19T17:08:12.000000Z",
                            "subcategories": [
                                {
                                    "category_id": 8,
                                    "parent_id": 6,
                                    "name": "فساتين",
                                    "slug": "dresses",
                                    "description": "Rerum voluptas sequi facere officiis. Aspernatur voluptate est et asperiores qui est eveniet est. Error aperiam sed non aut assumenda sequi numquam. Et temporibus rem et quam tempore.",
                                    "image_url": null,
                                    "status": "publish",
                                    "created_at": "2024-02-19T17:08:12.000000Z",
                                    "updated_at": "2024-02-19T17:08:12.000000Z",
                                    "subcategories": []
                                }
                            ]
                        }
                    ]
                }
            ]
        },
        {
            "category_id": 10,
            "parent_id": null,
            "name": "أحذية",
            "slug": "shoes",
            "description": "Repellat voluptatum vel consequatur hic saepe qui. Quaerat inventore quibusdam molestiae adipisci. Autem fugiat non qui nobis excepturi culpa.",
            "image_url": null,
            "status": "publish",
            "created_at": "2024-02-19T17:08:12.000000Z",
            "updated_at": "2024-02-20T17:42:44.000000Z",
            "subcategories": []
        }
    ],
    "meta": []
}
```


### Get category by id

**GET:** /categories/{category_id}

**Success Response:**

```
{
    "status": 200,
    "msg": "Ok",
    "server_time": "2024-02-20T17:46:31.475359Z",
    "data": {
        "category_id": 1,
        "parent_id": null,
        "name": "ملابس",
        "slug": "clothes",
        "description": "Dolorem vero inventore quas illo quia rem. Ratione fugiat laboriosam et laborum veniam. Eum sint odio molestiae commodi. Accusamus molestiae sint in qui dicta.",
        "image_url": null,
        "status": "publish",
        "created_at": "2024-02-19T17:08:12.000000Z",
        "updated_at": "2024-02-19T17:30:51.000000Z",
        "subcategories": []
    },
    "meta": []
}
```


### Get subcategories of a category

**GET:** /categories/{category_id}/subcategories

**Success Response:**

```
{
    "status": 200,
    "msg": "Ok",
    "server_time": "2024-02-20T17:48:14.717617Z",
    "data": {
        "category_id": 3,
        "parent_id": 1,
        "name": "أطفال",
        "slug": "kids",
        "description": "Repellendus quidem tempora ipsa tempore alias aspernatur voluptatem. Ex et eum iusto et culpa deserunt dolores. Qui ut esse libero assumenda cum. Dolor ad nam ad quia dolor.",
        "image_url": null,
        "status": "publish",
        "created_at": "2024-02-19T17:08:12.000000Z",
        "updated_at": "2024-02-19T17:08:12.000000Z",
        "subcategories": [
            {
                "category_id": 6,
                "parent_id": 3,
                "name": "بنات",
                "slug": "girls",
                "description": "Beatae sequi placeat aut sed non nemo quaerat. Possimus inventore ratione id voluptas. Aliquam quisquam est libero et est hic et.",
                "image_url": null,
                "status": "publish",
                "created_at": "2024-02-19T17:08:12.000000Z",
                "updated_at": "2024-02-19T17:08:12.000000Z",
                "subcategories": []
            }
        ]
    },
    "meta": []
}
```

### Get deleted categories

**GET:** /categories/trashed

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization | Bearer token | Yes |

**Success Response:**

```
{
    "status": 200,
    "msg": "Ok",
    "server_time": "2024-02-20T17:50:30.782299Z",
    "data": [
        {
            "category_id": 10,
            "parent_id": null,
            "name": "أحذية",
            "slug": "shoes",
            "description": null,
            "image_url": null,
            "status": "publish",
            "created_at": "2024-02-19T17:08:12.000000Z",
            "updated_at": "2024-02-20T17:50:21.000000Z",
            "subcategories": []
        }
    ],
    "meta": []
}
```


### Move category to trash

**DELETE:** /categories/{category_id}

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization | Bearer token | Yes |

**Success Response:**

```
{
    "status": 200,
    "msg": "تم نقل التصنيف إلى سلة المحذوفات",
    "server_time": "2024-02-20T12:58:57.484562Z",
    "data": [],
    "meta": []
}
```


### Restore category from trash

**POST:** /categories/{category_id}/restore

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization | Bearer token | Yes |

**Success Response:**

```
{
    "status": 200,
    "msg": "تم نقل التصنيف إلى سلة المحذوفات",
    "server_time": "2024-02-20T12:58:57.484562Z",
    "data": [],
    "meta": []
}
```


### Delete category completely from database

**DELETE:** /categories/{category_id}/force

**Request Header**
| Key | Value | Required |
|----------|----------|----------|
| Authorization | Bearer token | Yes |

**Success Response:**

```
{
    "status": 200,
    "msg": "تم حذف التصنيف بنجاح",
    "server_time": "2024-02-20T12:53:35.108610Z",
    "data": [],
    "meta": []
}
```
