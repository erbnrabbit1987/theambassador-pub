# Contributing to Festival Ticketing Platform

## Welcome

Thank you for your interest in contributing to the Festival Ticketing Platform! This document provides guidelines and instructions for contributing to the project.

---

## Getting Started

### Prerequisites
- Git
- Docker and Docker Compose
- Node.js 18+ (for frontend)
- Python 3.11+ (for backend)
- Basic understanding of the project (read the [README](FESTIVAL_TICKETING_README.md))

### Development Setup

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd festival-ticketing-platform
   ```

2. **Set up environment variables**
   ```bash
   # Backend
   cp backend/.env.example backend/.env
   # Edit backend/.env with your configuration
   
   # Frontend
   cp frontend/.env.example frontend/.env.local
   # Edit frontend/.env.local with your configuration
   ```

3. **Start development environment**
   ```bash
   docker-compose up -d
   ```

4. **Set up backend**
   ```bash
   cd backend
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   pip install -r requirements.txt
   alembic upgrade head
   ```

5. **Set up frontend**
   ```bash
   cd frontend
   npm install
   npm run dev
   ```

---

## Development Workflow

### Branch Strategy

We use **Git Flow**:
- `main`: Production-ready code
- `develop`: Development branch
- `feature/*`: New features
- `bugfix/*`: Bug fixes
- `hotfix/*`: Critical production fixes

### Creating a Feature

1. **Create a feature branch**
   ```bash
   git checkout develop
   git pull origin develop
   git checkout -b feature/your-feature-name
   ```

2. **Make your changes**
   - Write clean, readable code
   - Follow coding standards (see below)
   - Add tests for new functionality
   - Update documentation if needed

3. **Commit your changes**
   ```bash
   git add .
   git commit -m "feat(scope): description of changes"
   ```
   See [Commit Message Convention](#commit-message-convention) below.

4. **Push and create Pull Request**
   ```bash
   git push origin feature/your-feature-name
   ```
   Then create a Pull Request on GitHub targeting `develop`.

### Commit Message Convention

We follow the [Conventional Commits](https://www.conventionalcommits.org/) specification:

```
<type>(<scope>): <subject>

<body>

<footer>
```

#### Types
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style (formatting, no logic change)
- `refactor`: Code refactoring
- `test`: Adding or updating tests
- `chore`: Maintenance tasks

#### Examples
```
feat(ambassador): add code generation endpoint

Implement POST /api/v1/ambassadors/codes/generate with
quota validation and 24-hour expiration.

Closes #123
```

```
fix(ticket): validate code expiration during purchase

Prevent purchase with expired codes by checking
expires_at timestamp.

Fixes #456
```

---

## Coding Standards

### Python (Backend)

#### Style
- Follow **PEP 8** style guide
- Use **Black** for code formatting
- Use **Ruff** for linting
- Maximum line length: 100 characters

#### Type Hints
- Use type hints for all functions
- Use Pydantic models for API schemas
- Use SQLAlchemy models for database entities

#### Example
```python
from typing import Optional
from pydantic import BaseModel

class CodeGenerateRequest(BaseModel):
    event_id: str
    ticket_type_id: str
    discount_percent: Optional[float] = None

async def generate_code(
    ambassador_id: str,
    request: CodeGenerateRequest
) -> AmbassadorCode:
    """Generate a new ambassador code."""
    # Implementation
    pass
```

#### Testing
- Write unit tests with **pytest**
- Aim for >80% code coverage
- Use fixtures for test data
- Test both success and error cases

### TypeScript/React (Frontend)

#### Style
- Follow **ESLint** and **Prettier** configurations
- Use **TypeScript** strict mode
- Maximum line length: 100 characters

#### Components
- Use functional components with hooks
- Extract reusable logic into custom hooks
- Use TypeScript interfaces for props

#### Example
```typescript
interface CodeGeneratorProps {
  ambassadorId: string;
  onCodeGenerated: (code: string) => void;
}

export function CodeGenerator({
  ambassadorId,
  onCodeGenerated,
}: CodeGeneratorProps) {
  // Implementation
}
```

#### Testing
- Write unit tests with **Vitest** or **Jest**
- Use **React Testing Library** for component tests
- Test user interactions, not implementation details

---

## Code Review Process

### Before Submitting
- [ ] Code follows style guidelines
- [ ] All tests pass
- [ ] New tests added for new features
- [ ] Documentation updated
- [ ] No console.logs or debug code
- [ ] No commented-out code
- [ ] Commit messages follow convention

### Review Checklist
- Code quality and readability
- Test coverage
- Performance considerations
- Security implications
- Documentation completeness
- Error handling
- Edge cases considered

### Review Feedback
- Be constructive and respectful
- Explain the "why" behind suggestions
- Approve when ready, request changes when needed
- Respond to feedback promptly

---

## Testing

### Backend Tests
```bash
cd backend
pytest
pytest --cov=app tests/  # With coverage
```

### Frontend Tests
```bash
cd frontend
npm test
npm run test:coverage  # With coverage
```

### Integration Tests
```bash
# Run full test suite
docker-compose -f docker-compose.test.yml up --abort-on-container-exit
```

---

## Documentation

### Code Documentation
- Document complex functions and classes
- Use docstrings (Python) or JSDoc (TypeScript)
- Explain "why", not just "what"

### API Documentation
- Update OpenAPI/Swagger docs when adding endpoints
- Include request/response examples
- Document error codes

### User Documentation
- Update user guides when adding features
- Keep README files up to date
- Document breaking changes

---

## Database Migrations

### Creating Migrations
```bash
cd backend
alembic revision --autogenerate -m "description"
alembic upgrade head
```

### Migration Guidelines
- Always review auto-generated migrations
- Test migrations on sample data
- Never edit existing migrations (create new ones)
- Document complex migrations

---

## Security

### Best Practices
- Never commit secrets or API keys
- Use environment variables for sensitive data
- Validate all user inputs
- Use parameterized queries (ORM handles this)
- Follow OWASP guidelines
- Report security issues privately

### Reporting Security Issues
If you discover a security vulnerability, please email security@example.com instead of creating a public issue.

---

## Project Structure

See [Repository Structure](FESTIVAL_TICKETING_REPOSITORY_STRUCTURE.md) for detailed organization.

### Key Directories
- `backend/app/`: Backend application code
- `frontend/src/`: Frontend application code
- `docs/`: Documentation
- `infrastructure/`: Infrastructure as Code
- `tests/`: Test files

---

## Getting Help

### Resources
- [Product Overview](FESTIVAL_TICKETING_PRODUCT_OVERVIEW.md)
- [API Design](FESTIVAL_TICKETING_API_DESIGN.md)
- [Business Logic](FESTIVAL_TICKETING_BUSINESS_LOGIC.md)

### Communication
- Create an issue for bugs or feature requests
- Use discussions for questions
- Tag maintainers for urgent issues

---

## Code of Conduct

### Our Standards
- Be respectful and inclusive
- Welcome newcomers
- Focus on constructive feedback
- Respect different viewpoints

### Unacceptable Behavior
- Harassment or discrimination
- Trolling or inflammatory comments
- Personal attacks
- Publishing others' private information

---

## Recognition

Contributors will be recognized in:
- Project README
- Release notes
- Contributor list (if applicable)

---

## Questions?

If you have questions about contributing:
1. Check the documentation
2. Search existing issues
3. Create a new issue with the "question" label

Thank you for contributing! 🎉

---

**Last Updated**: 2024  
**Version**: 1.0
