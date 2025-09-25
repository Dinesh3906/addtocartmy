# API Integration Guide

This document outlines all the API integrations for the AddToCart Flutter application.

## Overview

The app integrates with multiple e-commerce platforms and services to provide comprehensive product search and comparison functionality:

1. **Amazon** - Real-Time Amazon Data API
2. **Flipkart** - Real-Time Flipkart Data API  
3. **Myntra** - Myntra Price History Tracker API
4. **Barcode Lookup** - Product identification by barcode
5. **SerpAPI** - Google Shopping search (optional)

## API Configuration

### Environment Variables

Add these to your `.env` file:

```env
# RapidAPI Configuration (Required)
RAPIDAPI_KEY=810d844034mshd67b2c159e98c47p1e33f4jsn34e21cc549d2
RAPIDAPI_HOST=real-time-amazon-data.p.rapidapi.com
FLIPKART_RAPIDAPI_HOST=real-time-flipkart-data2.p.rapidapi.com
MYNTRA_RAPIDAPI_HOST=myntra-price-history-tracker.p.rapidapi.com
BARCODE_RAPIDAPI_HOST=barcode-lookup.p.rapidapi.com

# Optional APIs
SERPAPI_KEY=your-serpapi-key-here
```

### Affiliate Configuration

Update these in `ApiConfigService`:

```dart
static const String amazonAffiliateTag = 'your-amazon-tag';
static const String flipkartAffiliateId = 'your-flipkart-id';
static const String myntraAffiliateParam = 'utm_source=yourapp&utm_medium=affiliate';
```

## API Details

### 1. Amazon API (Real-Time Amazon Data)

**Base URL:** `https://real-time-amazon-data.p.rapidapi.com`

**Available Endpoints:**
- `GET /search` - Search products
- `GET /product-details` - Get product details by ASIN
- `GET /products-by-category` - Get products by category
- `GET /product-reviews` - Get product reviews
- `GET /product-offers` - Get product offers

**Usage:**
```dart
final amazonService = AmazonApiService();
final products = await amazonService.searchProducts(
  keyword: 'smartphone',
  category: 'Electronics',
  maxResults: 20,
);
```

### 2. Flipkart API (Real-Time Flipkart Data)

**Base URL:** `https://real-time-flipkart-data2.p.rapidapi.com`

**Available Endpoints:**
- `GET /search` - Search products
- `GET /product` - Get single product data

**Usage:**
```dart
final flipkartService = FlipkartApiService();
final products = await flipkartService.searchProducts(
  keyword: 'smartphone',
  maxResults: 20,
);
```

### 3. Myntra API (Myntra Price History Tracker)

**Base URL:** `https://myntra-price-history-tracker.p.rapidapi.com`

**Available Endpoints:**
- `POST /tracker` - Track products and get price history

**Usage:**
```dart
final myntraService = MyntraApiService();
final products = await myntraService.searchProducts(
  keyword: 'shirt',
  category: 'fashion',
  maxResults: 20,
);
```

### 4. Barcode Lookup API

**Base URL:** `https://barcode-lookup.p.rapidapi.com`

**Available Endpoints:**
- `GET /v3/products` - Look up product by barcode

**Usage:**
```dart
final barcodeService = BarcodeApiService();
final product = await barcodeService.lookupByBarcode('049000028058');
```

### 5. SerpAPI (Google Shopping)

**Base URL:** `https://serpapi.com`

**Available Endpoints:**
- `GET /search` - Google Shopping search

**Usage:**
```dart
final serpApiService = SerpApiService();
final products = await serpApiService.searchGoogleShopping(
  query: 'smartphone',
  maxResults: 20,
);
```

## Multi-Platform Search

The `MultiPlatformService` orchestrates searches across all platforms:

```dart
final multiPlatformService = MultiPlatformService();
final products = await multiPlatformService.searchAllPlatforms(
  query: 'smartphone',
  category: 'Electronics',
  maxResults: 30,
);
```

This service:
- Searches Amazon, Flipkart, and Myntra simultaneously
- Normalizes product data into consistent format
- Sorts results by best value (price vs rating ratio)
- Handles platform-specific errors gracefully

## Error Handling

All API services implement comprehensive error handling:

```dart
try {
  final products = await amazonService.searchProducts(keyword: 'test');
} catch (e) {
  if (e.toString().contains('rate limit')) {
    // Handle rate limiting
  } else if (e.toString().contains('network')) {
    // Handle network errors
  } else {
    // Handle other errors
  }
}
```

## Rate Limiting

- **RapidAPI**: 100 requests per minute (configurable)
- **Request Timeout**: 10 seconds
- **Cache Timeout**: 15 minutes

## Testing APIs

Use the `ApiTestService` to validate all integrations:

```dart
final apiTestService = ApiTestService();

// Test all APIs
final results = await apiTestService.testAllApis();

// Quick health check
final isHealthy = await apiTestService.quickHealthCheck();
```

## Data Models

All APIs return normalized `Product` objects:

```dart
class Product {
  final String id;
  final String title;
  final double price;
  final String currency;
  final double rating;
  final int reviewCount;
  final Platform platform;
  final String imageUrl;
  final String productUrl;
  final bool inStock;
  final String? brand;
  final String? description;
  final double? originalPrice;
  final String category;
}
```

## Platform Integration

Products include platform information for deep linking:

```dart
enum Platform {
  amazon,    // Amazon app: in.amazon.mShop.android.shopping
  flipkart,  // Flipkart app: com.flipkart.android
  myntra,    // Myntra app: com.myntra.android
}
```

## Affiliate Links

All product URLs include affiliate tracking:

- **Amazon**: `?tag=your-amazon-tag`
- **Flipkart**: `?affid=your-flipkart-id`
- **Myntra**: `?utm_source=yourapp&utm_medium=affiliate`

## Security Considerations

1. **API Keys**: Never commit real API keys to version control
2. **Rate Limiting**: Implement client-side rate limiting
3. **Caching**: Cache responses to reduce API calls
4. **Error Handling**: Don't expose API errors to users
5. **Validation**: Validate all API responses before processing

## Troubleshooting

### Common Issues

1. **Invalid API Key**
   - Check `.env` file configuration
   - Verify API key is active on RapidAPI

2. **Rate Limiting**
   - Implement exponential backoff
   - Cache responses appropriately
   - Consider upgrading API plan

3. **No Results**
   - Check search query formatting
   - Verify category parameters
   - Test with known working queries

4. **Network Errors**
   - Implement retry logic
   - Check internet connectivity
   - Verify API endpoints are accessible

### Debug Mode

Enable debug logging in development:

```dart
// In main.dart
if (kDebugMode) {
  // Enable Dio logging
  _dio.interceptors.add(LogInterceptor(
    requestBody: true,
    responseBody: true,
  ));
}
```

## Performance Optimization

1. **Concurrent Requests**: Search multiple platforms simultaneously
2. **Caching**: Cache search results for 15 minutes
3. **Pagination**: Load results in batches
4. **Image Loading**: Use lazy loading for product images
5. **Debouncing**: Debounce search queries to reduce API calls

## Monitoring

Track API performance and errors:

```dart
// Log API calls
print('API Call: ${endpoint} - ${response.statusCode} - ${response.data?.length ?? 0} results');

// Track errors
if (response.statusCode != 200) {
  // Log error for monitoring
}
```

## Future Enhancements

1. **Additional Platforms**: Integrate more e-commerce APIs
2. **Price Tracking**: Implement price history and alerts
3. **Recommendations**: Add AI-powered product recommendations
4. **Reviews Aggregation**: Combine reviews from multiple platforms
5. **Inventory Tracking**: Real-time stock availability