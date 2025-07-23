# API Endpoints Documentation - Appointment Scheduler
Following Divyanshu's comprehensive documentation style

## Base URL
```
http://localhost:5000/api/v1
```

## Authentication Header
```
Authorization: Bearer <JWT_TOKEN>
```

---

## 🔐 Authentication Endpoints

### POST /auth/signup
**Description**: Register a new user account  
**Access**: Public  
**Method**: POST  

**Request Body**:
```json
{
    "email": "user@example.com",
    "password": "securePassword123",
    "first_name": "John",
    "last_name": "Doe",
    "phone": "555-0123",
    "role": "customer"
}
```

**Response Success (201)**:
```json
{
    "success": true,
    "message": "Account created successfully. Please check your email for verification.",
    "data": {
        "user": {
            "user_id": 1,
            "email": "user@example.com",
            "first_name": "John",
            "last_name": "Doe",
            "role": "customer",
            "is_verified": false
        }
    }
}
```

**Response Error (400)**:
```json
{
    "success": false,
    "error": "Email already exists",
    "code": "EMAIL_EXISTS"
}
```

---

### POST /auth/login
**Description**: Authenticate user and get JWT token  
**Access**: Public  
**Method**: POST  

**Request Body**:
```json
{
    "email": "user@example.com",
    "password": "securePassword123"
}
```

**Response Success (200)**:
```json
{
    "success": true,
    "message": "Login successful",
    "data": {
        "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
        "user": {
            "user_id": 1,
            "email": "user@example.com",
            "first_name": "John",
            "last_name": "Doe",
            "role": "customer",
            "is_verified": true
        }
    }
}
```

---

### POST /auth/verify-email
**Description**: Verify user email address  
**Access**: Public  
**Method**: POST  

**Request Body**:
```json
{
    "token": "verification_token_here"
}
```

---

### POST /auth/forgot-password
**Description**: Request password reset email  
**Access**: Public  
**Method**: POST  

**Request Body**:
```json
{
    "email": "user@example.com"
}
```

---

### POST /auth/logout
**Description**: Logout user (blacklist JWT token)  
**Access**: Authenticated  
**Method**: POST  

---

## 👥 User Management Endpoints

### GET /users/profile
**Description**: Get current user profile  
**Access**: Authenticated  
**Method**: GET  

**Response Success (200)**:
```json
{
    "success": true,
    "data": {
        "user_id": 1,
        "email": "user@example.com",
        "first_name": "John",
        "last_name": "Doe",
        "phone": "555-0123",
        "role": "customer",
        "created_at": "2024-01-01T00:00:00.000Z"
    }
}
```

---

### PUT /users/profile
**Description**: Update user profile  
**Access**: Authenticated  
**Method**: PUT  

**Request Body**:
```json
{
    "first_name": "John",
    "last_name": "Smith",
    "phone": "555-0456"
}
```

---

### PUT /users/change-password
**Description**: Change user password  
**Access**: Authenticated  
**Method**: PUT  

**Request Body**:
```json
{
    "current_password": "oldPassword123",
    "new_password": "newPassword456"
}
```

---

## 🏢 Business Management Endpoints

### GET /businesses
**Description**: Get businesses (admin only gets own, others get all active)  
**Access**: Authenticated  
**Method**: GET  

**Query Parameters**:
- `page` (optional): Page number (default: 1)
- `limit` (optional): Items per page (default: 10)
- `search` (optional): Search by business name

**Response Success (200)**:
```json
{
    "success": true,
    "data": {
        "businesses": [
            {
                "business_id": 1,
                "business_name": "Downtown Medical Clinic",
                "business_type": "clinic",
                "description": "Full-service medical clinic",
                "address": "123 Main St",
                "city": "Anytown",
                "state": "CA",
                "phone": "555-0100",
                "email": "info@clinic.com",
                "is_active": true
            }
        ],
        "pagination": {
            "current_page": 1,
            "total_pages": 1,
            "total_items": 1,
            "items_per_page": 10
        }
    }
}
```

---

### POST /businesses
**Description**: Create new business  
**Access**: Admin only  
**Method**: POST  

**Request Body**:
```json
{
    "business_name": "New Medical Practice",
    "business_type": "clinic",
    "description": "Specialized medical practice",
    "address": "456 Oak Ave",
    "city": "Somewhere",
    "state": "CA",
    "zip_code": "12345",
    "phone": "555-0200",
    "email": "info@newpractice.com",
    "timezone": "America/New_York"
}
```

---

### GET /businesses/:id
**Description**: Get specific business details  
**Access**: Authenticated  
**Method**: GET  

---

### PUT /businesses/:id
**Description**: Update business details  
**Access**: Admin (own business only)  
**Method**: PUT  

---

## 👨‍⚕️ Provider Management Endpoints

### GET /providers
**Description**: Get all providers  
**Access**: Authenticated  
**Method**: GET  

**Query Parameters**:
- `business_id` (optional): Filter by business
- `service_id` (optional): Filter by service offered
- `page`, `limit`, `search`: Standard pagination

**Response Success (200)**:
```json
{
    "success": true,
    "data": {
        "providers": [
            {
                "provider_id": 1,
                "user_id": 2,
                "business_id": 1,
                "first_name": "Dr. Sarah",
                "last_name": "Smith",
                "title": "Dr.",
                "specialization": "Family Medicine",
                "bio": "Experienced family physician",
                "is_active": true,
                "services": [
                    {
                        "service_id": 1,
                        "service_name": "General Consultation",
                        "duration_minutes": 30,
                        "price": 150.00
                    }
                ]
            }
        ]
    }
}
```

---

### POST /providers
**Description**: Add new provider to business  
**Access**: Admin only  
**Method**: POST  

**Request Body**:
```json
{
    "user_id": 2,
    "business_id": 1,
    "title": "Dr.",
    "specialization": "Cardiology",
    "bio": "Heart specialist with 15 years experience",
    "service_ids": [1, 2]
}
```

---

### GET /providers/:id
**Description**: Get specific provider details with schedule  
**Access**: Authenticated  
**Method**: GET  

---

### PUT /providers/:id
**Description**: Update provider information  
**Access**: Admin or Provider (own profile)  
**Method**: PUT  

---

### GET /providers/:id/availability
**Description**: Get provider availability for date range  
**Access**: Authenticated  
**Method**: GET  

**Query Parameters**:
- `start_date` (required): YYYY-MM-DD
- `end_date` (required): YYYY-MM-DD
- `service_id` (required): Service ID

**Response Success (200)**:
```json
{
    "success": true,
    "data": {
        "available_slots": [
            {
                "date": "2024-01-15",
                "start_time": "09:00:00",
                "end_time": "09:30:00",
                "display_time": "9:00 AM",
                "price": 150.00
            },
            {
                "date": "2024-01-15",
                "start_time": "09:45:00",
                "end_time": "10:15:00",
                "display_time": "9:45 AM",
                "price": 150.00
            }
        ]
    }
}
```

---

## 🛠️ Service Management Endpoints

### GET /services
**Description**: Get all services  
**Access**: Authenticated  
**Method**: GET  

**Query Parameters**:
- `business_id` (optional): Filter by business
- `provider_id` (optional): Filter by provider
- `active_only`: true/false (default: true)

---

### POST /services
**Description**: Create new service  
**Access**: Admin only  
**Method**: POST  

**Request Body**:
```json
{
    "business_id": 1,
    "service_name": "Cardiology Consultation",
    "description": "Specialized heart consultation",
    "duration_minutes": 45,
    "price": 200.00,
    "buffer_time_minutes": 15
}
```

---

### GET /services/:id
**Description**: Get specific service details  
**Access**: Authenticated  
**Method**: GET  

---

### PUT /services/:id
**Description**: Update service information  
**Access**: Admin only  
**Method**: PUT  

---

## 📅 Appointment Management Endpoints

### GET /appointments
**Description**: Get user appointments  
**Access**: Authenticated  
**Method**: GET  

**Query Parameters**:
- `status`: scheduled, confirmed, completed, cancelled
- `start_date`, `end_date`: Date range filter
- `provider_id` (optional): Filter by provider
- `customer_id` (optional): Filter by customer (admin/provider only)

**Response Success (200)**:
```json
{
    "success": true,
    "data": {
        "appointments": [
            {
                "appointment_id": 1,
                "customer_id": 1,
                "provider_id": 1,
                "service_id": 1,
                "business_id": 1,
                "appointment_date": "2024-01-15",
                "start_time": "10:00:00",
                "end_time": "10:30:00",
                "status": "confirmed",
                "notes": "Regular checkup",
                "total_price": 150.00,
                "customer_name": "John Doe",
                "provider_name": "Dr. Sarah Smith",
                "service_name": "General Consultation",
                "business_name": "Downtown Medical Clinic",
                "created_at": "2024-01-01T00:00:00.000Z"
            }
        ]
    }
}
```

---

### POST /appointments
**Description**: Book new appointment  
**Access**: Authenticated  
**Method**: POST  

**Request Body**:
```json
{
    "provider_id": 1,
    "service_id": 1,
    "appointment_date": "2024-01-15",
    "start_time": "10:00:00",
    "notes": "First visit"
}
```

**Response Success (201)**:
```json
{
    "success": true,
    "message": "Appointment booked successfully",
    "data": {
        "appointment": {
            "appointment_id": 1,
            "appointment_date": "2024-01-15",
            "start_time": "10:00:00",
            "status": "scheduled",
            "confirmation_code": "APT-001-2024"
        }
    }
}
```

---

### GET /appointments/:id
**Description**: Get specific appointment details  
**Access**: Authenticated (own appointments or admin/provider)  
**Method**: GET  

---

### PUT /appointments/:id
**Description**: Update appointment (reschedule, add notes)  
**Access**: Customer (own), Provider, Admin  
**Method**: PUT  

**Request Body**:
```json
{
    "appointment_date": "2024-01-16",
    "start_time": "11:00:00",
    "notes": "Rescheduled due to conflict"
}
```

---

### PUT /appointments/:id/status
**Description**: Update appointment status  
**Access**: Provider, Admin  
**Method**: PUT  

**Request Body**:
```json
{
    "status": "confirmed",
    "internal_notes": "Patient confirmed via phone"
}
```

---

### DELETE /appointments/:id
**Description**: Cancel appointment  
**Access**: Customer (own), Provider, Admin  
**Method**: DELETE  

**Request Body**:
```json
{
    "cancellation_reason": "Personal emergency"
}
```

---

## 🔔 Notification Endpoints

### GET /notifications
**Description**: Get user notifications  
**Access**: Authenticated  
**Method**: GET  

**Query Parameters**:
- `unread_only`: true/false
- `type`: appointment_reminder, appointment_confirmed, etc.
- `page`, `limit`: Standard pagination

---

### PUT /notifications/:id/read
**Description**: Mark notification as read  
**Access**: Authenticated  
**Method**: PUT  

---

### PUT /notifications/mark-all-read
**Description**: Mark all notifications as read  
**Access**: Authenticated  
**Method**: PUT  

---

## 📊 Analytics Endpoints (Admin Only)

### GET /analytics/dashboard
**Description**: Get dashboard analytics  
**Access**: Admin only  
**Method**: GET  

**Response Success (200)**:
```json
{
    "success": true,
    "data": {
        "total_appointments_today": 15,
        "total_revenue_today": 2250.00,
        "total_customers": 150,
        "total_providers": 5,
        "appointment_status_breakdown": {
            "scheduled": 8,
            "confirmed": 5,
            "completed": 2
        }
    }
}
```

---

### GET /analytics/revenue
**Description**: Get revenue analytics  
**Access**: Admin only  
**Method**: GET  

**Query Parameters**:
- `start_date`, `end_date`: Date range
- `provider_id` (optional): Specific provider
- `service_id` (optional): Specific service

---

## Error Response Format
All endpoints return errors in this format:
```json
{
    "success": false,
    "error": "Error message description",
    "code": "ERROR_CODE",
    "details": {
        "field": "Additional error details if applicable"
    }
}
```

## Common HTTP Status Codes
- `200`: Success
- `201`: Created
- `400`: Bad Request
- `401`: Unauthorized
- `403`: Forbidden
- `404`: Not Found
- `422`: Validation Error
- `429`: Too Many Requests
- `500`: Internal Server Error