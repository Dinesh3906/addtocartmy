# AddToCart Project Setup Guide

This guide will help you set up the complete AddToCart project with both Flutter frontend and NestJS backend.

## Prerequisites

### Required Software
- **Flutter SDK** ≥ 3.22.0 ([Installation Guide](https://docs.flutter.dev/get-started/install))
- **Node.js** ≥ 18.0.0 ([Download](https://nodejs.org/))
- **Redis** ([Installation Guide](https://redis.io/docs/getting-started/installation/))
- **Git** ([Download](https://git-scm.com/downloads))

### Development Tools (Recommended)
- **VS Code** with Flutter and Dart extensions
- **Android Studio** for Android development
- **Xcode** (macOS only) for iOS development
- **Docker** (optional, for containerized development)

## Quick Start

### 1. Clone and Setup

```bash
# Clone the repository
git clone <repository-url>
cd addtocart

# Setup Flutter app
cd addtocart_flutter
cp .env.example .env
flutter pub get

# Setup Backend
cd ../addtocart_backend
cp .env.example .env
npm install
```

### 2. Start Services

#### Option A: Using Docker (Recommended)
```bash
# Start Redis and Backend
docker-compose up -d

# The backend will be available at http://localhost:3000
# API docs at http://localhost:3000/api/docs
```

#### Option B: Manual Setup
```bash
# Terminal 1: Start Redis
redis-server

# Terminal 2: Start Backend
cd addtocart_backend
npm run start:dev

# Terminal 3: Start Flutter (after backend is running)
cd addtocart_flutter
flutter run
```

### 3. Verify Setup

1. **Backend Health Check**
   ```bash
   curl http://localhost:3000/api/auth/session/validate
   ```

2. **Flutter App**
   - Should launch and show splash screen
   - Navigate through onboarding flow
   - Reach home screen with category selection

## Detailed Setup Instructions

### Flutter App Configuration

1. **Environment Variables** (`.env` file):
   ```env
   API_BASE_URL=http://localhost:3000/api
   USE_MOCK_DATA=true
   ENABLE_ANALYTICS=false
   ```

2. **Generate Code**:
   ```bash
   flutter packages pub run build_runner build --delete-conflicting-outputs
   ```

3. **Run Tests**:
   ```bash
   flutter test
   flutter test integration_test/
   ```

### Backend Configuration

1. **Environment Variables** (`.env` file):
   ```env
   PORT=3000
   NODE_ENV=development
   REDIS_HOST=localhost
   REDIS_PORT=6379
   ENABLE_MOCK_MODE=true
   
   # API Keys (optional for mock mode)
   AMAZON_ACCESS_KEY=your-key
   FLIPKART_AFFILIATE_ID=your-id
   LLM_API_KEY=your-key
   ```

2. **Database Setup**:
   ```bash
   # Redis must be running
   redis-cli ping  # Should return PONG
   ```

3. **Run Tests**:
   ```bash
   npm run test
   npm run test:e2e
   ```

## Development Workflow

### 1. Feature Development

```bash
# Create feature branch
git checkout -b feature/new-feature

# Make changes to Flutter app
cd addtocart_flutter
# ... make changes ...
flutter test

# Make changes to backend
cd ../addtocart_backend
# ... make changes ...
npm run test

# Commit and push
git add .
git commit -m "Add new feature"
git push origin feature/new-feature
```

### 2. Code Quality

```bash
# Flutter
cd addtocart_flutter
dart format .
flutter analyze
flutter test --coverage

# Backend
cd addtocart_backend
npm run lint
npm run format
npm run test:cov
```

### 3. Mock Data Development

The project includes comprehensive mock data for development without real API keys:

- **Categories**: `mock_data/categories.json`
- **Products**: `mock_data/products.json`
- **Backend Mock Mode**: Set `ENABLE_MOCK_MODE=true`

## API Integration

### Amazon Product Advertising API v5

1. **Get API Credentials**:
   - Sign up for Amazon Associates program
   - Request PA-API access
   - Get Access Key, Secret Key, and Associate Tag

2. **Configure**:
   ```env
   AMAZON_ACCESS_KEY=your-access-key
   AMAZON_SECRET_KEY=your-secret-key
   AMAZON_ASSOCIATE_TAG=your-associate-tag
   ENABLE_MOCK_MODE=false
   ```

### Flipkart Affiliate API

1. **Get API Credentials**:
   - Join Flipkart Affiliate program
   - Get Affiliate ID and Token

2. **Configure**:
   ```env
   FLIPKART_AFFILIATE_ID=your-affiliate-id
   FLIPKART_AFFILIATE_TOKEN=your-token
   ```

### AI/LLM Service

1. **OpenAI API** (recommended):
   ```env
   LLM_API_KEY=your-openai-key
   LLM_BASE_URL=https://api.openai.com/v1
   LLM_MODEL=gpt-3.5-turbo
   ```

## Troubleshooting

### Common Issues

1. **Flutter build fails**:
   ```bash
   flutter clean
   flutter pub get
   flutter packages pub run build_runner clean
   flutter packages pub run build_runner build --delete-conflicting-outputs
   ```

2. **Backend Redis connection fails**:
   ```bash
   # Check Redis is running
   redis-cli ping
   
   # Check port availability
   netstat -an | grep 6379
   ```

3. **API calls fail**:
   - Verify backend is running on port 3000
   - Check `.env` configuration
   - Enable mock mode for development

4. **Android emulator network issues**:
   ```env
   # Use 10.0.2.2 for Android emulator
   API_BASE_URL=http://10.0.2.2:3000/api
   ```

### Performance Issues

1. **Slow Flutter builds**:
   ```bash
   flutter clean
   flutter pub deps
   # Use --debug for faster development builds
   flutter run --debug
   ```

2. **Backend memory usage**:
   - Check Redis memory usage
   - Monitor Node.js heap size
   - Adjust cache TTL values

## Production Deployment

### Flutter App

1. **Android**:
   ```bash
   flutter build apk --release
   flutter build appbundle --release
   ```

2. **iOS**:
   ```bash
   flutter build ios --release
   ```

### Backend

1. **Docker**:
   ```bash
   docker build -t addtocart-backend ./addtocart_backend
   docker run -p 3000:3000 --env-file .env addtocart-backend
   ```

2. **Node.js**:
   ```bash
   npm run build
   npm run start:prod
   ```

## Support

### Documentation
- **API Docs**: http://localhost:3000/api/docs (when backend is running)
- **Flutter Docs**: Check `addtocart_flutter/README.md`
- **Backend Docs**: Check `addtocart_backend/README.md`

### Getting Help
1. Check this setup guide
2. Review error logs
3. Check GitHub issues
4. Verify environment configuration

### Useful Commands

```bash
# Check versions
flutter --version
node --version
redis-server --version

# Reset everything
flutter clean && flutter pub get
rm -rf addtocart_backend/node_modules && npm install

# View logs
docker-compose logs -f backend
flutter logs
```