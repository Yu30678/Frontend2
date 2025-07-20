# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Chinese e-commerce web application with separate member and admin interfaces. The frontend consists of static HTML pages with embedded JavaScript that communicate with a Maven-based backend via REST APIs.

## 技術棧
- Frontend: httpServer + javascript
- Backend: java + maven
- Database: MySQL
- Styling: Tailwind CSS  
  
## 註記  
所有解釋內容以繁體中文顯示  

**Member APIs:**
- `POST /member/login` - Member authentication (returns member_id and optional token)
- `POST /member/register` - Member registration
- `GET /product` - Product listing
- `GET /cart` - Cart retrieval (uses GET with query parameter)
- `POST /cart` - Add items to cart
- `PUT /cart` - Update cart item quantities  
- `DELETE /cart` - Remove items from cart

**Image Service:**
- `GET localhost:8081/images/{filename}` - Static image serving (separate Docker service)  

### 後端文件參考  
- member類別  
  /Users/changyu/IdeaProjects/Backend_side_project/src/main/java/controller/memberController.java  
  /Users/changyu/IdeaProjects/Backend_side_project/src/main/java/dao/memberDAO.java  
- cart類別  
  /Users/changyu/IdeaProjects/Backend_side_project/src/main/java/controller/cartController.java  
  /Users/changyu/IdeaProjects/Backend_side_project/src/main/java/dao/cartDAO.java  
- user類別
  /Users/changyu/IdeaProjects/Backend_side_project/src/main/java/controller/userController.java
  /Users/changyu/IdeaProjects/Backend_side_project/src/main/java/dao

### State Management
- **Member ID**: Stored in `localStorage.memberId` after login, used for all cart operations
- **Authentication**: Optional token stored in `localStorage.authToken`
- **Member Info**: Name and email stored in localStorage for display

### Key Implementation Details

**Cart API Pattern:**
The cart functionality uses a specific pattern where it first attempts GET with query parameters (`/cart?member_id=123`), then falls back to POST with JSON body if needed. This accommodates the Maven backend's GET method preference.

**Image URL Handling:**
Images are normalized to use the format `http://localhost:8081/images/{filename}` regardless of how they're stored in the backend.

**Error Handling:**
Each page includes comprehensive error handling with user-friendly Chinese messages and fallback mechanisms (e.g., test data when APIs are unavailable).

## Development Commands

Currently this is a static frontend project with minimal build requirements:

```bash
# No build process required - serve HTML files directly
# Example using Python's built-in server:
python -m http.server 8000

# Or using Node.js http-server:
npx http-server .
```

## Common Development Tasks

**Testing Cart Functionality:**
The cart API expects the backend to accept GET requests with member_id as a query parameter. Test with: `GET /cart?member_id=111`

**Adding New Products:**
Products are fetched from `/product` endpoint and expect this JSON structure:
```json
{
  "data": [
    {
      "product_id": 1,
      "name": "Product Name",
      "price": 29900,
      "soh": 50,
      "category_name": "Category",
      "image_url": "filename.png"
    }
  ]
}
```

**Member Session Flow:**
1. Login via `memberlogin.html` stores member_id in localStorage
2. Dashboard reads member_id and uses it for all API calls
3. Cart operations require member_id parameter
4. Logout clears localStorage and redirects to login

## Backend Requirements

- Maven-based REST API server on port 8080
- Image service on port 8081 (typically Docker container)
- CORS configuration to allow frontend requests
- GET method support for cart queries with query parameters
- Chinese language support for error messages

## Known Issues

- Mobile functionality has been intentionally removed from member-dashboard.html
- Test data fallbacks are available when backend APIs are unavailable
- Image loading includes fallback emoji icons when images fail to load