# Changelog

All notable changes to SaveMate will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [1.0.0] - 2026-01-04

### Added

#### Core Features
- Complete authentication system with JWT tokens
- User registration and login with email + username
- Password hashing with bcrypt (12 rounds)
- JWT access tokens (30 min) and refresh tokens (7 days)
- Protected routes and role-based access control

#### Business Features
- Business profile creation and management
- Multi-step business verification system
- Document upload for verification (business license, tax ID, proof of address)
- Business dashboard with analytics
- Deal creation and management (CRUD operations)
- Performance tracking (views, saves, redemptions)

#### User Features
- User dashboard and profile management
- Deal discovery and browsing
- Advanced search and filtering
- Category-based browsing (20 categories)
- Favorites system (save deals)
- Location-based deal discovery
- Mobile-responsive design with bottom navigation

#### Platform Features
- Admin dashboard for user and business management
- Verification approval workflow
- Image upload via Cloudinary CDN
- 20 organized deal categories with icons
- Review and rating system
- Email notifications (planned)

#### Technical Implementation
- FastAPI backend (Python 3.11+)
- React frontend (18.2+) with Vite
- MongoDB database with Beanie ODM
- Tailwind CSS for styling
- Context API for state management
- React Router for navigation
- Axios for HTTP requests

#### Documentation
- Complete project documentation (31 files)
- API reference with 45+ endpoints
- System architecture documentation
- Database schema documentation
- Deployment guides
- Development guides
- Component documentation
- Testing documentation

### Security
- Bcrypt password hashing with salt
- JWT token authentication
- CORS protection
- Input validation with Pydantic
- SQL injection prevention (NoSQL with ODM)
- XSS prevention (React auto-escaping)
- Environment variable management

### Performance
- Database indexing (unique, geo-spatial, text search)
- Image optimization via Cloudinary
- Lazy loading for images
- API response caching (planned)
- Code splitting (planned)

### Testing
- E2E testing suite with Playwright (59 tests)
- Unit tests for backend
- API integration tests
- Mobile responsiveness tests
- Cross-browser compatibility tests

---

## [0.9.0] - 2025-12-15

### Added
- Initial project setup
- Basic authentication endpoints
- Database models and schemas
- Frontend scaffolding
- Basic UI components

### Changed
- Improved API structure
- Enhanced security measures
- Updated database schema

### Fixed
- Login validation issues
- CORS configuration
- Database connection handling

---

## [0.8.0] - 2025-12-01

### Added
- Deal management system
- Category implementation
- Business profile pages
- User favorites feature

### Changed
- Restructured frontend components
- Improved error handling
- Updated API responses

---

## [0.7.0] - 2025-11-15

### Added
- Business verification workflow
- Document upload functionality
- Admin dashboard
- Verification status tracking

### Changed
- Enhanced business profile structure
- Improved verification UI

### Fixed
- File upload errors
- Verification status updates

---

## [0.6.0] - 2025-11-01

### Added
- Search functionality
- Deal filtering
- Location-based search
- Category filtering

### Changed
- Improved search performance
- Updated search UI

---

## [0.5.0] - 2025-10-15

### Added
- Mobile-responsive design
- Bottom navigation for mobile
- Touch-optimized interactions
- Responsive grid layouts

### Changed
- Updated mobile breakpoints
- Improved mobile UX

---

## [0.4.0] - 2025-10-01

### Added
- Cloudinary integration
- Image upload functionality
- Image transformation
- CDN delivery

### Changed
- Updated image handling
- Improved upload UI

---

## [0.3.0] - 2025-09-15

### Added
- User dashboard
- Business dashboard
- Analytics tracking
- Performance metrics

### Changed
- Enhanced dashboard UI
- Updated analytics display

---

## [0.2.0] - 2025-09-01

### Added
- Basic deal CRUD operations
- Deal listing page
- Deal detail page
- Deal creation form

### Changed
- Improved deal validation
- Updated deal schema

---

## [0.1.0] - 2025-08-15

### Added
- Initial project structure
- Basic API setup
- Database connection
- Authentication skeleton

---

## Upcoming Features (Roadmap)

### Q1 2026
- [ ] Performance optimization
- [ ] Security audit
- [ ] Beta testing
- [ ] Production deployment

### Q2 2026
- [ ] React Native mobile apps
- [ ] iOS App Store submission
- [ ] Google Play submission
- [ ] Push notifications
- [ ] Offline mode

### Q3 2026
- [ ] Payment processing integration
- [ ] Advanced analytics dashboard
- [ ] AI-powered deal recommendations
- [ ] Social features
- [ ] Multi-language support

### Q4 2026
- [ ] International expansion
- [ ] White-label solution
- [ ] Partner API
- [ ] Enterprise features
- [ ] Advanced reporting

---

## Version History Summary

| Version | Date | Type | Description |
|---------|------|------|-------------|
| 1.0.0 | 2026-01-04 | Major | Production-ready release |
| 0.9.0 | 2025-12-15 | Minor | Feature complete |
| 0.8.0 | 2025-12-01 | Minor | Deal management |
| 0.7.0 | 2025-11-15 | Minor | Business verification |
| 0.6.0 | 2025-11-01 | Minor | Search functionality |
| 0.5.0 | 2025-10-15 | Minor | Mobile responsive |
| 0.4.0 | 2025-10-01 | Minor | Image handling |
| 0.3.0 | 2025-09-15 | Minor | Dashboards |
| 0.2.0 | 2025-09-01 | Minor | Deal CRUD |
| 0.1.0 | 2025-08-15 | Minor | Initial release |

---

## Breaking Changes

### Version 1.0.0
- None (first major release)

### Version 0.9.0
- Updated authentication flow to use refresh tokens
- Changed API response format for consistency
- Modified database schema for businesses

---

## Deprecations

### Version 1.0.0
- None

---

## Migration Guides

### Migrating from 0.9.x to 1.0.0

No breaking changes. Simply update dependencies:

```bash
# Backend
pip install --upgrade -r requirements.txt

# Frontend
npm update
```

---

## Contributors

- **SaveMate Team** - Initial work and ongoing development
- **Pawel** - Team Lead & Full Stack Development
- **WSB University** - Academic support

---

## Links

- [GitHub Repository](https://github.com/jenfranx30/savemate-docs)
- [Documentation](https://github.com/jenfranx30/savemate-docs)
- [Issue Tracker](https://github.com/jenfranx30/savemate-docs/issues)
- [Frontend Repo](https://github.com/jenfranx30/savemate-frontend)
- [Backend Repo](https://github.com/jenfranx30/savemate-backend)

---

**Last Updated:** January 4, 2026  
**Current Version:** 1.0.0  
**Status:** Production Ready
