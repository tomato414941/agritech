# Component Design

## Frontend Components

### Core Components

#### Layout Components
```typescript
// AppLayout.tsx
interface AppLayoutProps {
    children: React.ReactNode;
}

const AppLayout: React.FC<AppLayoutProps> = ({ children }) => {
    return (
        <Box>
            <Header />
            <Sidebar />
            <main>{children}</main>
            <Footer />
        </Box>
    );
};
```

#### Navigation Components
```typescript
// Sidebar.tsx
interface NavItem {
    label: string;
    icon: IconType;
    path: string;
}

const Sidebar: React.FC = () => {
    const navItems: NavItem[] = [
        { label: 'Dashboard', icon: FiHome, path: '/' },
        { label: 'Fields', icon: FiMap, path: '/fields' },
        { label: 'Crops', icon: FiGrid, path: '/crops' },
        { label: 'Reports', icon: FiBarChart2, path: '/reports' }
    ];

    return (
        <VStack>
            {navItems.map(item => (
                <NavItem key={item.path} {...item} />
            ))}
        </VStack>
    );
};
```

### Feature Components

#### Field Management
```typescript
// FieldMap.tsx
interface FieldMapProps {
    fieldId: string;
    onBoundaryChange?: (boundary: GeoJSON.Polygon) => void;
}

const FieldMap: React.FC<FieldMapProps> = ({ fieldId, onBoundaryChange }) => {
    return (
        <MapContainer>
            <FieldBoundary fieldId={fieldId} />
            <FieldMarkers fieldId={fieldId} />
            {onBoundaryChange && <BoundaryEditor onChange={onBoundaryChange} />}
        </MapContainer>
    );
};
```

#### Crop Planning
```typescript
// CropPlanner.tsx
interface CropPlannerProps {
    fieldId: string;
}

const CropPlanner: React.FC<CropPlannerProps> = ({ fieldId }) => {
    return (
        <Box>
            <CropCalendar fieldId={fieldId} />
            <CropList fieldId={fieldId} />
            <AddCropForm fieldId={fieldId} />
        </Box>
    );
};
```

### Data Display Components

#### Charts and Graphs
```typescript
// WeatherChart.tsx
interface WeatherChartProps {
    fieldId: string;
    dateRange: DateRange;
}

const WeatherChart: React.FC<WeatherChartProps> = ({ fieldId, dateRange }) => {
    return (
        <LineChart>
            <XAxis dataKey="date" />
            <YAxis />
            <Line dataKey="temperature" stroke="#8884d8" />
            <Line dataKey="rainfall" stroke="#82ca9d" />
        </LineChart>
    );
};
```

## Backend Components

### Core Services

#### Authentication Service
```python
class AuthService:
    async def authenticate_user(self, email: str, password: str) -> User:
        user = await self.user_repository.get_by_email(email)
        if not user or not verify_password(password, user.password_hash):
            raise InvalidCredentialsError()
        return user

    async def create_access_token(self, user: User) -> str:
        data = {
            "sub": str(user.id),
            "email": user.email,
            "exp": datetime.utcnow() + timedelta(minutes=30)
        }
        return jwt.encode(data, settings.jwt_secret_key, algorithm="HS256")
```

#### Field Service
```python
class FieldService:
    async def create_field(self, user_id: UUID, field_data: FieldCreate) -> Field:
        field = Field(
            user_id=user_id,
            name=field_data.name,
            boundary=field_data.boundary,
            area=calculate_area(field_data.boundary)
        )
        return await self.field_repository.create(field)

    async def get_field_details(self, field_id: UUID) -> FieldDetails:
        field = await self.field_repository.get_by_id(field_id)
        crops = await self.crop_repository.get_by_field_id(field_id)
        soil_data = await self.soil_repository.get_latest(field_id)
        return FieldDetails(field=field, crops=crops, soil_data=soil_data)
```

### Data Models

#### Base Model
```python
class BaseModel(SQLAlchemyBaseModel):
    __abstract__ = True

    id: UUID = Column(UUID(as_uuid=True), primary_key=True, default=uuid4)
    created_at: datetime = Column(DateTime(timezone=True), server_default=func.now())
    updated_at: datetime = Column(DateTime(timezone=True), onupdate=func.now())
```

#### Field Model
```python
class Field(BaseModel):
    __tablename__ = "fields"

    user_id: UUID = Column(UUID(as_uuid=True), ForeignKey("users.id"), nullable=False)
    name: str = Column(String, nullable=False)
    boundary: Geometry = Column(Geometry("POLYGON"), nullable=False)
    area: float = Column(Float, nullable=False)
    soil_type: str = Column(String)
    metadata: dict = Column(JSONB, default={})

    crops: List["Crop"] = relationship("Crop", back_populates="field")
    soil_data: List["SoilData"] = relationship("SoilData", back_populates="field")
```

### API Routes

#### Field Routes
```python
@router.post("/fields/", response_model=FieldResponse)
async def create_field(
    field_data: FieldCreate,
    current_user: User = Depends(get_current_user),
    field_service: FieldService = Depends()
):
    field = await field_service.create_field(current_user.id, field_data)
    return FieldResponse.from_orm(field)

@router.get("/fields/{field_id}", response_model=FieldDetails)
async def get_field(
    field_id: UUID,
    current_user: User = Depends(get_current_user),
    field_service: FieldService = Depends()
):
    return await field_service.get_field_details(field_id)
```

## Integration Points

### External Services Integration

#### Weather Service
```python
class WeatherService:
    async def get_forecast(self, latitude: float, longitude: float) -> WeatherForecast:
        async with httpx.AsyncClient() as client:
            response = await client.get(
                f"{settings.weather_api_url}/forecast",
                params={
                    "lat": latitude,
                    "lon": longitude,
                    "appid": settings.weather_api_key
                }
            )
            data = response.json()
            return WeatherForecast.parse_obj(data)
```

#### Map Service
```typescript
class MapService {
    async getFieldBoundary(fieldId: string): Promise<GeoJSON.Polygon> {
        const response = await api.get(`/fields/${fieldId}/boundary`);
        return response.data.boundary;
    }

    async updateFieldBoundary(fieldId: string, boundary: GeoJSON.Polygon): Promise<void> {
        await api.put(`/fields/${fieldId}/boundary`, { boundary });
    }
}
```

## State Management

### Frontend State
```typescript
interface FieldStore {
    fields: Field[];
    selectedField: Field | null;
    loading: boolean;
    error: Error | null;

    fetchFields: () => Promise<void>;
    selectField: (fieldId: string) => void;
    updateField: (field: Field) => Promise<void>;
}

const useFieldStore = create<FieldStore>((set) => ({
    fields: [],
    selectedField: null,
    loading: false,
    error: null,

    fetchFields: async () => {
        set({ loading: true });
        try {
            const fields = await api.get('/fields');
            set({ fields, loading: false });
        } catch (error) {
            set({ error, loading: false });
        }
    }
}));
```

## Error Handling

### Backend Error Handling
```python
class AppException(Exception):
    def __init__(self, message: str, status_code: int = 400):
        self.message = message
        self.status_code = status_code

@app.exception_handler(AppException)
async def app_exception_handler(request: Request, exc: AppException):
    return JSONResponse(
        status_code=exc.status_code,
        content={"error": exc.message}
    )
```

### Frontend Error Handling
```typescript
const ErrorBoundary: React.FC<{ children: React.ReactNode }> = ({ children }) => {
    const [error, setError] = useState<Error | null>(null);

    if (error) {
        return (
            <ErrorAlert
                title="An error occurred"
                message={error.message}
                onRetry={() => setError(null)}
            />
        );
    }

    return (
        <ErrorBoundary fallback={<ErrorFallback />}>
            {children}
        </ErrorBoundary>
    );
};
```
