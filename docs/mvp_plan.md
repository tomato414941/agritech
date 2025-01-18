# AgriTech MVP Development Plan

## Overview

The AgriTech MVP focuses on two core concepts: crop reference data and actual cultivation records.
We will start with simple features and evolve gradually based on user feedback.

## Related Documents
- [System Architecture](architecture/system_architecture.md)
- [Database Schema](architecture/database_schema.md)

## Core Concepts

### 1. Crop
Reference data defining general characteristics and cultivation requirements for agricultural crops

- Basic Information
  - Crop Name
  - Category (Fruit Vegetables, Leafy Vegetables, etc.)
  - Standard Growth Period
  - Optimal Growing Season

### 2. Cultivation
Records of actual cultivation activities

- Basic Information
  - Crop Selection
  - Start Date
  - Optional Name (e.g., "Spring Tomatoes")
  - Status (Active, Completed, Abandoned)

## MVP Features

### 1. Crop Selection
- Selection from Crop Master Data
- Display Basic Information
- Start Cultivation Function

### 2. Cultivation Management
- List of Active Cultivations
- Days Elapsed Display
- Status Management
- Reference Information Access

### 3. LLM Support
- Detailed Crop Information
- Timely Advice
- Basic Question Handling

## Development Approach

1. Initial Implementation with Minimal Features
2. User Feedback Collection
3. Gradual Feature Enhancement
4. Continuous Usability Improvement

## Mobile Strategy

### Phase 1: Responsive Web Application
- Develop as a responsive web application
- Ensure mobile-friendly UI/UX
- Test across different devices

### Phase 2: PWA Implementation
- Add Service Workers
- Implement offline support
- Enable installation capability

### Phase 3: Native Features (Based on User Feedback)
- Consider React Native migration
- Or adopt Capacitor/Cordova
- Optimize for platform-specific features
