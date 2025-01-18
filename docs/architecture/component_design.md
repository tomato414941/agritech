# Component Design

## Frontend Components

### Core Components

#### Layout Components
```typescript
// AppLayout.tsx
interface AppLayoutProps {
    children: React.ReactNode;
    sidebarCollapsed?: boolean;
}

const AppLayout: React.FC<AppLayoutProps> = ({ 
    children, 
    sidebarCollapsed = false 
}) => {
    return (
        <Box className="app-layout">
            <Header className="app-header" />
            <Sidebar 
                className="app-sidebar"
                collapsed={sidebarCollapsed} 
            />
            <main className="app-main">
                {children}
            </main>
            <Footer className="app-footer" />
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
    children?: NavItem[];
}

const Sidebar: React.FC = () => {
    const navItems: NavItem[] = [
        { 
            label: 'Dashboard', 
            icon: FiHome, 
            path: '/' 
        },
        { 
            label: 'Fields', 
            icon: FiMap, 
            path: '/fields',
            children: [
                { 
                    label: 'Map View', 
                    icon: FiMap, 
                    path: '/fields/map' 
                },
                { 
                    label: 'List View', 
                    icon: FiList, 
                    path: '/fields/list' 
                }
            ]
        },
        { 
            label: 'Crops', 
            icon: FiGrid, 
            path: '/crops' 
        },
        { 
            label: 'Reports', 
            icon: FiBarChart2, 
            path: '/reports' 
        }
    ];

    return (
        <VStack spacing={2} align="stretch">
            {navItems.map(item => (
                <NavItem 
                    key={item.path} 
                    {...item} 
                />
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
    readOnly?: boolean;
}

const FieldMap: React.FC<FieldMapProps> = ({ 
    fieldId, 
    onBoundaryChange,
    readOnly = false 
}) => {
    const [field, setField] = useState<Field | null>(null);
    const [loading, setLoading] = useState(true);

    useEffect(() => {
        const loadField = async () => {
            try {
                const data = await fetchField(fieldId);
                setField(data);
            } catch (error) {
                console.error('Failed to load field:', error);
            } finally {
                setLoading(false);
            }
        };

        loadField();
    }, [fieldId]);

    return (
        <MapContainer>
            {loading ? (
                <LoadingSpinner />
            ) : (
                <>
                    <FieldBoundary 
                        boundary={field?.boundary} 
                        editable={!readOnly}
                    />
                    <FieldMarkers 
                        markers={field?.markers} 
                    />
                    {!readOnly && onBoundaryChange && (
                        <BoundaryEditor 
                            onChange={onBoundaryChange} 
                        />
                    )}
                </>
            )}
        </MapContainer>
    );
};
```

#### Crop Planning
```typescript
// CropPlanner.tsx
interface CropPlannerProps {
    fieldId: string;
    year: number;
}

const CropPlanner: React.FC<CropPlannerProps> = ({ fieldId, year }) => {
    const [crops, setCrops] = useState<Crop[]>([]);
    const [schedule, setSchedule] = useState<CropSchedule[]>([]);

    const validateCropRotation = (newCrop: Crop): boolean => {
        const previousCrop = crops[crops.length - 1];
        return isValidRotation(previousCrop, newCrop);
    };

    return (
        <Box className="crop-planner">
            <CropCalendar 
                schedule={schedule}
                onScheduleChange={setSchedule}
            />
            <CropList 
                crops={crops}
                onCropSelect={handleCropSelect}
            />
            <AddCropForm 
                fieldId={fieldId}
                onValidate={validateCropRotation}
                onSubmit={handleAddCrop}
            />
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
    metrics: string[];  // e.g., ['temperature', 'rainfall']
}

const WeatherChart: React.FC<WeatherChartProps> = ({ 
    fieldId, 
    dateRange,
    metrics 
}) => {
    const [data, setData] = useState<WeatherData[]>([]);

    useEffect(() => {
        const fetchWeatherData = async () => {
            const weatherData = await getFieldWeather(
                fieldId,
                dateRange.start,
                dateRange.end
            );
            setData(weatherData);
        };

        fetchWeatherData();
    }, [fieldId, dateRange]);

    return (
        <Box className="weather-chart">
            <LineChart data={data}>
                <XAxis 
                    dataKey="date" 
                    type="time" 
                />
                <YAxis yAxisId="temp" />
                <YAxis yAxisId="rain" orientation="right" />
                {metrics.includes('temperature') && (
                    <Line 
                        dataKey="temperature" 
                        stroke="#8884d8"
                        yAxisId="temp"
                    />
                )}
                {metrics.includes('rainfall') && (
                    <Line 
                        dataKey="rainfall" 
                        stroke="#82ca9d"
                        yAxisId="rain"
                    />
                )}
                <Tooltip />
                <Legend />
            </LineChart>
            <DateRangeSelector 
                range={dateRange}
                onChange={handleDateRangeChange}
            />
        </Box>
    );
};
```

## Backend Components

### Core Services

#### Authentication Service
```python
class AuthService:
    async def authenticate_user(
        self, 
        email: str, 
        password: str
    ) -> User:
        user = await self.user_repository.get_by_email(email)
        if not user or not verify_password(password, user.password_hash):
            raise InvalidCredentialsError()
        return user

    async def create_access_token(
        self, 
        user: User,
        expires_delta: Optional[timedelta] = None
    ) -> str:
        data = {
            "sub": str(user.id),
            "email": user.email,
            "exp": datetime.utcnow() + (
                expires_delta or timedelta(minutes=30)
            )
        }
        return jwt.encode(data, settings.jwt_secret_key, algorithm="HS256")
```

#### Field Service
```python
class FieldService:
    async def create_field(
        self, 
        user_id: UUID, 
        field_data: FieldCreate
    ) -> Field:
        field = Field(
            user_id=user_id,
            name=field_data.name,
            boundary=field_data.boundary,
            area=calculate_area(field_data.boundary)
        )
        return await self.field_repository.create(field)

    async def get_field_details(
        self, 
        field_id: UUID
    ) -> FieldDetails:
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
