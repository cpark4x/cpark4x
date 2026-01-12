# Workspaces Patterns

Common patterns and conventions for Canvas/Workspaces2 development.

---

## Frontend Patterns

### Component Structure

```typescript
// Standard functional component
import { useState } from 'react'
import { Button } from '@/components/ui/button'

interface MyComponentProps {
  title: string
  onAction: () => void
}

export function MyComponent({ title, onAction }: MyComponentProps) {
  const [loading, setLoading] = useState(false)
  const [error, setError] = useState<Error | null>(null)
  
  // Loading state
  if (loading) {
    return <div className="flex items-center justify-center p-4">
      <div className="animate-spin">Loading...</div>
    </div>
  }
  
  // Error state
  if (error) {
    return <div className="text-red-500 p-4">
      Error: {error.message}
    </div>
  }
  
  // Main content
  return (
    <div className="space-y-4">
      <h2 className="text-xl font-semibold">{title}</h2>
      <Button onClick={onAction}>Action</Button>
    </div>
  )
}
```

### Zustand Store Pattern

```typescript
// src/store/myFeatureStore.ts
import { create } from 'zustand'

interface MyFeatureState {
  data: Data | null
  loading: boolean
  error: Error | null
  
  // Actions
  fetchData: () => Promise<void>
  clearError: () => void
}

export const useMyFeatureStore = create<MyFeatureState>((set, get) => ({
  data: null,
  loading: false,
  error: null,
  
  fetchData: async () => {
    set({ loading: true, error: null })
    try {
      const response = await fetch('/api/v1/my-endpoint')
      const data = await response.json()
      set({ data, loading: false })
    } catch (error) {
      set({ error: error as Error, loading: false })
    }
  },
  
  clearError: () => set({ error: null })
}))
```

### API Service Pattern

```typescript
// src/services/myFeatureService.ts
import { apiClient } from './apiClient'

export interface MyFeatureRequest {
  param: string
}

export interface MyFeatureResponse {
  result: string
}

export const myFeatureService = {
  getData: async (params: MyFeatureRequest): Promise<MyFeatureResponse> => {
    const response = await apiClient.get<MyFeatureResponse>(
      '/api/v1/my-endpoint',
      { params }
    )
    return response.data
  },
  
  createData: async (data: MyFeatureRequest): Promise<MyFeatureResponse> => {
    const response = await apiClient.post<MyFeatureResponse>(
      '/api/v1/my-endpoint',
      data
    )
    return response.data
  }
}
```

### Tailwind Styling Conventions

```typescript
// Use consistent spacing and layout utilities
<div className="space-y-4">           {/* Vertical spacing */}
<div className="flex items-center gap-2">  {/* Horizontal layout */}
<div className="p-4 rounded-lg border">   {/* Container */}
<h1 className="text-2xl font-bold">       {/* Typography */}
<Button className="bg-primary text-white"> {/* Theme colors */}
```

---

## Backend Patterns

### FastAPI Endpoint Pattern

```python
# backend/app/api/v1/endpoints/my_feature.py
from fastapi import APIRouter, Depends, HTTPException
from sqlalchemy.orm import Session

from app.api import deps
from app.models.user import User
from app.schemas.my_feature import MyFeatureRequest, MyFeatureResponse
from app.services.my_feature_service import MyFeatureService

router = APIRouter()

@router.get("/my-endpoint", response_model=MyFeatureResponse)
async def get_my_data(
    param: str,
    current_user: User = Depends(deps.get_current_user),
    db: Session = Depends(deps.get_db)
) -> MyFeatureResponse:
    """
    Get my data.
    
    This endpoint achieves: [outcome description]
    """
    service = MyFeatureService(db)
    result = service.get_data(user_id=current_user.id, param=param)
    
    if not result:
        raise HTTPException(status_code=404, detail="Data not found")
    
    return result

@router.post("/my-endpoint", response_model=MyFeatureResponse)
async def create_my_data(
    request: MyFeatureRequest,
    current_user: User = Depends(deps.get_current_user),
    db: Session = Depends(deps.get_db)
) -> MyFeatureResponse:
    """
    Create my data.
    
    This endpoint achieves: [outcome description]
    """
    service = MyFeatureService(db)
    result = service.create_data(
        user_id=current_user.id,
        data=request
    )
    
    return result
```

### Pydantic Schema Pattern

```python
# backend/app/schemas/my_feature.py
from pydantic import BaseModel, Field
from typing import Optional
from datetime import datetime

class MyFeatureBase(BaseModel):
    """Base schema with common fields."""
    name: str = Field(..., min_length=1, max_length=255)
    description: Optional[str] = None

class MyFeatureRequest(MyFeatureBase):
    """Request schema (create/update)."""
    pass

class MyFeatureResponse(MyFeatureBase):
    """Response schema (read)."""
    id: int
    user_id: int
    created_at: datetime
    updated_at: datetime
    
    class Config:
        from_attributes = True  # SQLAlchemy model compatibility
```

### Service Layer Pattern

```python
# backend/app/services/my_feature_service.py
from sqlalchemy.orm import Session
from typing import Optional

from app.models.my_feature import MyFeature
from app.schemas.my_feature import MyFeatureRequest, MyFeatureResponse

class MyFeatureService:
    """Service layer for MyFeature business logic."""
    
    def __init__(self, db: Session):
        self.db = db
    
    def get_data(self, user_id: int, param: str) -> Optional[MyFeatureResponse]:
        """Get data for user."""
        result = self.db.query(MyFeature).filter(
            MyFeature.user_id == user_id,
            MyFeature.param == param
        ).first()
        
        if not result:
            return None
        
        return MyFeatureResponse.from_orm(result)
    
    def create_data(
        self,
        user_id: int,
        data: MyFeatureRequest
    ) -> MyFeatureResponse:
        """Create new data."""
        db_obj = MyFeature(
            user_id=user_id,
            **data.dict()
        )
        self.db.add(db_obj)
        self.db.commit()
        self.db.refresh(db_obj)
        
        return MyFeatureResponse.from_orm(db_obj)
```

### SQLAlchemy Model Pattern

```python
# backend/app/models/my_feature.py
from sqlalchemy import Column, Integer, String, ForeignKey, DateTime
from sqlalchemy.orm import relationship
from datetime import datetime

from app.db.base_class import Base

class MyFeature(Base):
    """MyFeature database model."""
    
    __tablename__ = "my_features"
    
    id = Column(Integer, primary_key=True, index=True)
    user_id = Column(Integer, ForeignKey("users.id"), nullable=False)
    name = Column(String(255), nullable=False)
    description = Column(String, nullable=True)
    created_at = Column(DateTime, default=datetime.utcnow, nullable=False)
    updated_at = Column(DateTime, default=datetime.utcnow, onupdate=datetime.utcnow, nullable=False)
    
    # Relationships
    user = relationship("User", back_populates="my_features")
```

---

## Testing Patterns

### Frontend Component Test

```typescript
// frontend/src/components/__tests__/MyComponent.test.tsx
import { render, screen, fireEvent } from '@testing-library/react'
import { MyComponent } from '../MyComponent'

describe('MyComponent', () => {
  it('renders with title', () => {
    render(<MyComponent title="Test" onAction={() => {}} />)
    expect(screen.getByText('Test')).toBeInTheDocument()
  })
  
  it('calls onAction when button clicked', () => {
    const onAction = jest.fn()
    render(<MyComponent title="Test" onAction={onAction} />)
    
    fireEvent.click(screen.getByText('Action'))
    expect(onAction).toHaveBeenCalled()
  })
  
  it('shows loading state', () => {
    // Test loading state rendering
  })
  
  it('shows error state', () => {
    // Test error state rendering
  })
})
```

### Backend Endpoint Test

```python
# backend/tests/api/v1/test_my_feature.py
import pytest
from fastapi.testclient import TestClient
from sqlalchemy.orm import Session

from app.core.config import settings
from app.tests.utils.utils import get_test_user_token

def test_get_my_data(client: TestClient, db: Session):
    """Test getting data."""
    # Setup
    token = get_test_user_token(client)
    headers = {"Authorization": f"Bearer {token}"}
    
    # Execute
    response = client.get(
        f"{settings.API_V1_STR}/my-endpoint?param=test",
        headers=headers
    )
    
    # Assert
    assert response.status_code == 200
    data = response.json()
    assert data["name"] is not None

def test_create_my_data(client: TestClient, db: Session):
    """Test creating data."""
    # Setup
    token = get_test_user_token(client)
    headers = {"Authorization": f"Bearer {token}"}
    payload = {"name": "Test", "description": "Test description"}
    
    # Execute
    response = client.post(
        f"{settings.API_V1_STR}/my-endpoint",
        json=payload,
        headers=headers
    )
    
    # Assert
    assert response.status_code == 200
    data = response.json()
    assert data["name"] == "Test"
    assert data["id"] is not None

def test_unauthorized_access(client: TestClient):
    """Test that endpoint requires authentication."""
    response = client.get(f"{settings.API_V1_STR}/my-endpoint")
    assert response.status_code == 401
```

---

## Common Utilities

### Error Handling

```typescript
// Frontend
try {
  await myFeatureService.getData(params)
} catch (error) {
  console.error('Failed to fetch data:', error)
  toast.error('Failed to load data. Please try again.')
}
```

```python
# Backend
from app.core.exceptions import BadRequestException, NotFoundException

# Raise specific exceptions
if not data:
    raise NotFoundException("Data not found")

if invalid_param:
    raise BadRequestException("Invalid parameter")
```

### Loading States

```typescript
// Frontend - always show loading and error states
function MyFeature() {
  const { data, loading, error } = useMyFeature()
  
  if (loading) return <LoadingSpinner />
  if (error) return <ErrorMessage error={error} />
  if (!data) return <EmptyState />
  
  return <Content data={data} />
}
```

### Authentication

```typescript
// Frontend - protected routes
import { ProtectedRoute } from '@/components/auth/ProtectedRoute'

<Route path="/my-feature" element={
  <ProtectedRoute>
    <MyFeature />
  </ProtectedRoute>
} />
```

```python
# Backend - protected endpoints
from app.api import deps

@router.get("/protected")
async def protected_endpoint(
    current_user: User = Depends(deps.get_current_user)
):
    # current_user is authenticated User object
    return {"message": f"Hello {current_user.email}"}
```

---

## File Naming Conventions

### Frontend
- Components: `PascalCase.tsx` (e.g., `MyFeature.tsx`)
- Hooks: `useCamelCase.ts` (e.g., `useMyFeature.ts`)
- Utilities: `camelCase.ts` (e.g., `formatDate.ts`)
- Types: `PascalCase.types.ts` (e.g., `MyFeature.types.ts`)

### Backend
- Endpoints: `snake_case.py` (e.g., `my_feature.py`)
- Models: `snake_case.py` (e.g., `my_feature.py`)
- Schemas: `snake_case.py` (e.g., `my_feature.py`)
- Services: `snake_case_service.py` (e.g., `my_feature_service.py`)

---

## Integration Patterns

### Frontend → Backend Flow

1. User interaction triggers action
2. Component calls service
3. Service makes API request
4. Backend endpoint validates and processes
5. Service returns data
6. Store updates (if using Zustand)
7. Component re-renders with new data

### Adding New Feature Checklist

**Backend:**
- [ ] Create model in `app/models/`
- [ ] Create schemas in `app/schemas/`
- [ ] Create service in `app/services/`
- [ ] Create endpoints in `app/api/v1/endpoints/`
- [ ] Register router in `app/api/v1/api.py`
- [ ] Add tests in `tests/api/v1/`

**Frontend:**
- [ ] Create service in `src/services/`
- [ ] Create store if needed in `src/store/`
- [ ] Create components in `src/components/`
- [ ] Create page if needed in `src/pages/`
- [ ] Add route in `src/App.tsx`
- [ ] Add tests in `__tests__/`

---

Use these patterns as starting points. Adapt as needed but stay consistent with existing Canvas code.
