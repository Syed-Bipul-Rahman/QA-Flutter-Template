# ApiService Usage Guide

This document provides examples of how to use the `ApiService` class for making HTTP requests using different methods: GET, POST, PUT, PATCH, and file uploads.

## Table of Contents
- [Initializing ApiService](#initializing-apiservice)
- [GET Requests](#get-requests)
- [POST Requests](#post-requests)
- [PUT Requests](#put-requests)
- [PATCH Requests](#patch-requests)
- [File Uploads](#file-uploads)
- [Error Handling](#error-handling)
- [Advanced Options](#advanced-options)

## Initializing ApiService

The `ApiService` follows the singleton pattern. You can get an instance like this:

```dart
final apiService = ApiService();
```

In most cases, you'll want to use dependency injection to provide the service to your classes:

```dart
// In your repository or service class
class UserRepository {
  final ApiService _apiService;
  
  UserRepository({required ApiService apiService}) : _apiService = apiService;
}
```

## GET Requests

### Basic GET Request
```dart
Future<void> fetchUsers() async {
  try {
    final response = await _apiService.get('/api/users');
    // Process the response data
    final users = response.data;
    print('Users: $users');
  } catch (e) {
    print('Error: $e');
  }
}
```

### GET Request with Query Parameters
```dart
Future<void> fetchUsersWithFilters({int page = 1, String search = ''}) async {
  try {
    final response = await _apiService.get(
      '/api/users',
      queryParameters: {
        'page': page,
        'search': search,
      },
    );
    // Process the response data
    final users = response.data;
    print('Filtered Users: $users');
  } catch (e) {
    print('Error: $e');
  }
}
```

### GET Request with Custom Options
```dart
Future<void> fetchUserDataWithAuth() async {
  try {
    final options = Options(
      headers: {
        'Authorization': 'Bearer your_auth_token',
        'Content-Type': 'application/json',
      },
    );
    
    final response = await _apiService.get(
      '/api/user/profile',
      options: options,
    );
    // Process the response data
    final userData = response.data;
    print('User Data: $userData');
  } catch (e) {
    print('Error: $e');
  }
}
```

## POST Requests

### Basic POST Request
```dart
Future<void> createUser(Map<String, dynamic> userData) async {
  try {
    final response = await _apiService.post(
      '/api/users',
      data: userData,
    );
    // Process the response data
    final createdUser = response.data;
    print('Created User: $createdUser');
  } catch (e) {
    print('Error: $e');
  }
}
```

### POST Request with Authentication and Headers
```dart
Future<void> createPost(Map<String, dynamic> postData) async {
  try {
    final options = Options(
      headers: {
        'Authorization': 'Bearer your_auth_token',
        'Content-Type': 'application/json',
      },
    );
    
    final response = await _apiService.post(
      '/api/posts',
      data: postData,
      options: options,
    );
    // Process the response data
    final createdPost = response.data;
    print('Created Post: $createdPost');
  } catch (e) {
    print('Error: $e');
  }
}
```

### POST Request with Query Parameters
```dart
Future<void> submitForm(Map<String, dynamic> formData) async {
  try {
    final response = await _apiService.post(
      '/api/form',
      data: formData,
      queryParameters: {'version': 'v2'},
    );
    // Process the response data
    final result = response.data;
    print('Form Submission Result: $result');
  } catch (e) {
    print('Error: $e');
  }
}
```

## PUT Requests

### Complete Resource Update
```dart
Future<void> updateUser(int userId, Map<String, dynamic> updatedData) async {
  try {
    final response = await _apiService.put(
      '/api/users/$userId',
      data: updatedData,
    );
    // Process the response data
    final updatedUser = response.data;
    print('Updated User: $updatedUser');
  } catch (e) {
    print('Error: $e');
  }
}
```

### PUT Request with Custom Headers
```dart
Future<void> updateProduct(int productId, Map<String, dynamic> productData) async {
  try {
    final options = Options(
      headers: {
        'Authorization': 'Bearer admin_token',
        'Content-Type': 'application/json',
      },
    );
    
    final response = await _apiService.put(
      '/api/products/$productId',
      data: productData,
      options: options,
    );
    // Process the response data
    final updatedProduct = response.data;
    print('Updated Product: $updatedProduct');
  } catch (e) {
    print('Error: $e');
  }
}
```

## PATCH Requests

### Partial Resource Update
```dart
Future<void> updateUserName(int userId, String newName) async {
  try {
    final response = await _apiService.patch(
      '/api/users/$userId',
      data: {'name': newName},
    );
    // Process the response data
    final updatedUser = response.data;
    print('Updated User: $updatedUser');
  } catch (e) {
    print('Error: $e');
  }
}
```

### PATCH Request with Authorization
```dart
Future<void> updateOrderStatus(int orderId, String status) async {
  try {
    final options = Options(
      headers: {
        'Authorization': 'Bearer merchant_token',
        'Content-Type': 'application/json',
      },
    );

    final response = await _apiService.patch(
      '/api/orders/$orderId',
      data: {'status': status},
      options: options,
    );
    // Process the response data
    final updatedOrder = response.data;
    print('Updated Order: $updatedOrder');
  } catch (e) {
    print('Error: $e');
  }
}
```

## File Uploads

### Uploading a Single File
```dart
import 'dart:io';
import 'package:dio/dio.dart';

Future<void> uploadUserProfileImage(File imageFile) async {
  try {
    // Create MultipartFile from File
    final multipartFile = await MultipartFile.fromFile(
      imageFile.path,
      filename: 'profile_image.jpg',
    );

    final response = await _apiService.uploadFile(
      '/api/users/upload-profile-image',
      file: multipartFile,
      fieldName: 'image', // The field name expected by the server
    );

    // Process the response
    print('Upload response: ${response.data}');
  } catch (e) {
    print('Upload error: $e');
  }
}
```

### Uploading a Single File with Additional Data
```dart
import 'dart:io';
import 'package:dio/dio.dart';

Future<void> uploadUserDocument(File documentFile, int userId) async {
  try {
    final multipartFile = await MultipartFile.fromFile(
      documentFile.path,
      filename: 'document.pdf',
    );

    final response = await _apiService.uploadFile(
      '/api/users/$userId/upload-document',
      file: multipartFile,
      fieldName: 'document',
      data: {
        'document_type': 'identity',
        'user_id': userId.toString(),
      },
    );

    print('Document upload response: ${response.data}');
  } catch (e) {
    print('Document upload error: $e');
  }
}
```

### Uploading Multiple Files
```dart
import 'dart:io';
import 'package:dio/dio.dart';

Future<void> uploadMultipleImages(List<File> imageFiles) async {
  try {
    // Convert each file to MultipartFile
    final multipartFiles = <MultipartFile>[];
    for (int i = 0; i < imageFiles.length; i++) {
      final file = imageFiles[i];
      final multipartFile = await MultipartFile.fromFile(
        file.path,
        filename: 'image_$i.jpg',
      );
      multipartFiles.add(multipartFile);
    }

    final response = await _apiService.uploadMultipleFiles(
      '/api/gallery/upload-images',
      files: multipartFiles,
      fieldName: 'images',
    );

    print('Multiple upload response: ${response.data}');
  } catch (e) {
    print('Multiple upload error: $e');
  }
}
```

### Uploading Multiple Files with Additional Data
```dart
import 'dart:io';
import 'package:dio/dio.dart';

Future<void> uploadGalleryWithMetadata(
  List<File> imageFiles,
  String galleryName,
  String description,
) async {
  try {
    final multipartFiles = <MultipartFile>[];
    for (int i = 0; i < imageFiles.length; i++) {
      final file = imageFiles[i];
      final multipartFile = await MultipartFile.fromFile(
        file.path,
        filename: 'gallery_image_$i.jpg',
      );
      multipartFiles.add(multipartFile);
    }

    final response = await _apiService.uploadMultipleFiles(
      '/api/gallery/create',
      files: multipartFiles,
      fieldName: 'images',
      data: {
        'name': galleryName,
        'description': description,
        'category': 'event',
      },
    );

    print('Gallery upload response: ${response.data}');
  } catch (e) {
    print('Gallery upload error: $e');
  }
}
```

### Progress Tracking During Upload
```dart
import 'dart:io';
import 'package:dio/dio.dart';

Future<void> uploadFileWithProgress(File file) async {
  try {
    final multipartFile = await MultipartFile.fromFile(
      file.path,
      filename: 'file.pdf',
    );

    final response = await _apiService.uploadFileWithProgress(
      '/api/upload',
      file: multipartFile,
      onSendProgress: (int sent, int total) {
        double progress = (sent / total) * 100;
        print('Upload progress: $progress%');
      },
      options: Options(
        headers: {
          'Authorization': 'Bearer your_token',
        },
      ),
    );

    print('Upload response: ${response.data}');
  } catch (e) {
    print('Upload error: $e');
  }
}
```

## Error Handling

The `ApiService` provides custom exception handling. Here's how to handle different types of errors:

```dart
Future<void> handleApiCall() async {
  try {
    final response = await _apiService.get('/api/data');
    // Process the response data
    print('Success: ${response.data}');
  } on TimeoutException {
    // Handle timeout errors
    print('Request timed out');
  } on NoInternetException {
    // Handle network connectivity errors
    print('No internet connection');
  } on ServerException catch (e) {
    // Handle server errors (4xx, 5xx status codes)
    print('Server error: ${e.message}, Status: ${e.statusCode}');
  } on UnknownException {
    // Handle other unexpected errors
    print('Unknown error occurred');
  } catch (e) {
    // Handle any other unexpected errors
    print('Unexpected error: $e');
  }
}
```

## Advanced Options

### Canceling Requests
```dart
CancelToken cancelToken = CancelToken();

Future<void> makeCancelableRequest() async {
  try {
    final response = await _apiService.get(
      '/api/large-data',
      cancelToken: cancelToken,
    );
    print('Response: ${response.data}');
  } catch (e) {
    if (CancelToken.isCancel(e)) {
      print('Request was cancelled');
    } else {
      print('Error: $e');
    }
  }
}

// To cancel the request
void cancelRequest() {
  cancelToken.cancel('Request cancelled by user');
}
```

### Using with Repository Pattern
```dart
class UserRepository {
  final ApiService _apiService;
  
  UserRepository({required ApiService apiService}) : _apiService = apiService;
  
  Future<List<User>> getUsers() async {
    try {
      final response = await _apiService.get('/api/users');
      final List<dynamic> usersList = response.data;
      return usersList.map((json) => User.fromJson(json)).toList();
    } catch (e) {
      // Handle or rethrow the exception
      rethrow;
    }
  }
  
  Future<User> createUser(User user) async {
    try {
      final response = await _apiService.post(
        '/api/users',
        data: user.toJson(),
      );
      return User.fromJson(response.data);
    } catch (e) {
      // Handle or rethrow the exception
      rethrow;
    }
  }
  
  Future<User> updateUser(int userId, User user) async {
    try {
      final response = await _apiService.put(
        '/api/users/$userId',
        data: user.toJson(),
      );
      return User.fromJson(response.data);
    } catch (e) {
      // Handle or rethrow the exception
      rethrow;
    }
  }
  
  Future<void> updateUserName(int userId, String newName) async {
    try {
      await _apiService.patch(
        '/api/users/$userId',
        data: {'name': newName},
      );
    } catch (e) {
      // Handle or rethrow the exception
      rethrow;
    }
  }
}
```