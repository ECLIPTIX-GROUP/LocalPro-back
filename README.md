# LocalPro.AI Backend

## Overview

LocalPro.AI is a prospecting and marketing automation platform designed for local entrepreneurs in Africa. The platform enables users to discover business prospects, manage contacts through a CRM system, and execute multi-channel marketing campaigns via WhatsApp, Email, and SMS. The system includes AI-powered assistance for campaign optimization and prospect research strategies.

The backend is built with Django and Django REST Framework, providing a comprehensive API for prospect management, CRM operations, campaign execution, and analytics tracking.

## User Preferences

Preferred communication style: Simple, everyday language.

## System Architecture

### Backend Framework
- **Django 5.2** with **Django REST Framework** for RESTful API development
- **Problem**: Need a robust, scalable backend framework with built-in admin capabilities and ORM
- **Solution**: Django provides batteries-included development with excellent ORM, authentication, and admin interface
- **Pros**: Mature ecosystem, strong security features, excellent documentation, built-in admin panel
- **Cons**: Monolithic structure, can be opinionated about certain patterns

### Authentication & Authorization
- **JWT-based authentication** using `djangorestframework-simplejwt`
- **Problem**: Stateless authentication needed for API access across multiple clients
- **Solution**: JSON Web Tokens for secure, scalable authentication without server-side session storage
- **Custom User Model** extending Django's AbstractUser with subscription and usage tracking fields
- Subscription plans control feature access (prospects per month, messages per month)

### Application Structure
The backend follows Django's app-based modular architecture with five main modules:

1. **Users Module** (`localpro_backend.users`)
   - Custom user model with subscription plan management
   - Registration, login, and profile management
   - AI integration endpoints for chat and message optimization
   - Tracks monthly usage limits (prospects_used_this_month, messages_sent_this_month)

2. **Prospects Module** (`localpro_backend.prospects`)
   - Business prospect discovery and storage
   - Geolocation support (latitude/longitude for mapping)
   - Multiple data sources (Google Maps, Facebook, LinkedIn, directories, manual)
   - Search functionality with sector and city filtering
   - Mock prospect generation for testing

3. **CRM Module** (`localpro_backend.crm`)
   - Contact management system
   - Contact status pipeline (New → Contacted → Interested → Negotiation → Client → Inactive)
   - Segment creation for contact grouping
   - CSV export functionality
   - GeoJSON support for map visualization
   - Ability to convert prospects into CRM contacts

4. **Campaigns Module** (`localpro_backend.campaigns`)
   - Multi-channel marketing campaigns (WhatsApp, Email, SMS)
   - Campaign lifecycle management (Draft → Scheduled → Sent → Completed/Failed)
   - Recipient tracking with delivery status
   - Campaign analytics (sent, opened, responded counts)
   - Subscription-based message limits enforcement

5. **Analytics Module** (`localpro_backend.analytics`)
   - Dashboard statistics tracking
   - Daily metrics aggregation per user
   - Performance metrics (response rates, conversion tracking)
   - Historical data for trend analysis

### Data Architecture
- **Relational Database** using Django ORM (database-agnostic, typically PostgreSQL in production)
- **User-centric data isolation**: All major models have ForeignKey relationships to User
- **JSON fields** for flexible data storage (tags, filters, segments)
- **Geospatial data** support via DecimalField for coordinates
- **Audit trails** with created_at/updated_at timestamps on all models

### API Design
- **RESTful architecture** with ViewSets and Routers
- **Filtering, searching, and ordering** via django-filter and DRF filters
- **Pagination** for list endpoints (Django REST Framework default)
- **Permission classes**: IsAuthenticated for protected endpoints, AllowAny for public endpoints
- **Custom actions** using @action decorator for specialized operations (send campaigns, export CSV, etc.)

### Cross-Origin Resource Sharing (CORS)
- **django-cors-headers** middleware for frontend-backend communication
- Configured to allow cross-origin requests for API consumption

### AI Integration
- **OpenAI GPT-4o-mini** integration for AI-powered features
- Three context modes: general assistance, campaign optimization, prospect strategy
- AI chat endpoint for user queries
- Message optimization endpoint for improving campaign content
- Error handling for missing API keys

### Subscription Model
- **Tiered subscription plans**: Starter, Business, Pro+, Enterprise
- **Usage limits** enforced at the API level:
  - Prospects per month
  - Campaign messages per month
- **Monthly reset logic** for usage counters
- Plan details stored in settings (SUBSCRIPTION_PLANS configuration)

### Configuration Management
- **python-decouple** for environment variable management
- Separation of sensitive data (SECRET_KEY, OPENAI_API_KEY, database credentials)
- Debug mode controlled via environment

### Static and Media Files
- Static files configuration for serving logos and assets
- Media files support for user uploads
- Development mode static file serving

## External Dependencies

### Third-Party Python Packages
- **djangorestframework**: REST API framework
- **djangorestframework-simplejwt**: JWT authentication
- **django-cors-headers**: CORS middleware
- **django-filter**: Advanced filtering for querysets
- **python-decouple**: Environment configuration management
- **openai**: OpenAI API client for AI features
- **pandas**: Data manipulation and CSV export (imported in CRM views)

### External APIs
- **OpenAI API** (GPT-4o-mini model)
  - Used for AI assistant features
  - Campaign message optimization
  - Prospect research assistance
  - Requires OPENAI_API_KEY environment variable

### Database
- Django ORM configured (database engine not specified in settings.py excerpt)
- Supports PostgreSQL, MySQL, SQLite based on Django configuration
- Migrations managed via Django's migration system

### Future Integration Points
- **WhatsApp Business API**: For sending WhatsApp campaigns
- **Email Service Provider**: SMTP or transactional email service (SendGrid, Mailgun)
- **SMS Gateway**: For SMS campaign delivery
- **Google Maps API**: For enhanced prospect location data
- **Facebook/LinkedIn APIs**: For social media prospect scraping
- **Payment Gateway**: For subscription management (likely Mobile Money for African market)
