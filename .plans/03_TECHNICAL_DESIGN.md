# Technical Design Specification
## Closed-Loop Diabetes Management System

**Document Version:** 1.0
**Date:** 2025-11-23
**Status:** Draft

---

## Table of Contents
1. [Backend Service Design](#1-backend-service-design)
2. [Mobile Application Design](#2-mobile-application-design)
3. [Database Schema Design](#3-database-schema-design)
4. [API Design](#4-api-design)
5. [Device Communication Protocol](#5-device-communication-protocol)
6. [Algorithm Implementation](#6-algorithm-implementation)
7. [Testing Strategy](#7-testing-strategy)

---

## 1. Backend Service Design

### 1.1 Project Structure (Python)

```
backend/
├── src/
│   ├── api/
│   │   ├── __init__.py
│   │   ├── deps.py                 # Dependencies (DB, auth)
│   │   ├── routes/
│   │   │   ├── __init__.py
│   │   │   ├── auth.py
│   │   │   ├── users.py
│   │   │   ├── devices.py
│   │   │   ├── data.py
│   │   │   ├── predictions.py
│   │   │   └── alerts.py
│   │   └── middleware/
│   │       ├── auth.py
│   │       ├── logging.py
│   │       └── error_handlers.py
│   ├── core/
│   │   ├── __init__.py
│   │   ├── config.py               # Settings (Pydantic BaseSettings)
│   │   ├── security.py             # JWT, password hashing
│   │   ├── logging.py              # Structured logging
│   │   └── exceptions.py           # Custom exceptions
│   ├── models/
│   │   ├── __init__.py
│   │   ├── user.py                 # SQLAlchemy models
│   │   ├── device.py
│   │   ├── glucose_reading.py
│   │   ├── insulin_delivery.py
│   │   ├── meal.py
│   │   └── alert.py
│   ├── schemas/
│   │   ├── __init__.py
│   │   ├── user.py                 # Pydantic schemas
│   │   ├── device.py
│   │   ├── glucose.py
│   │   ├── insulin.py
│   │   └── alert.py
│   ├── services/
│   │   ├── __init__.py
│   │   ├── user_service.py
│   │   ├── device_service.py
│   │   ├── glucose_service.py
│   │   ├── algorithm_service.py
│   │   ├── alert_service.py
│   │   └── notification_service.py
│   ├── ml/
│   │   ├── __init__.py
│   │   ├── models/
│   │   │   ├── glucose_predictor.py
│   │   │   ├── meal_absorption.py
│   │   │   └── exercise_impact.py
│   │   ├── inference/
│   │   │   ├── predictor.py
│   │   │   └── dosing_calculator.py
│   │   ├── training/
│   │   │   ├── trainer.py
│   │   │   └── data_loader.py
│   │   └── utils/
│   │       ├── feature_engineering.py
│   │       └── evaluation.py
│   ├── integrations/
│   │   ├── __init__.py
│   │   ├── dexcom/
│   │   │   ├── client.py
│   │   │   └── adapter.py
│   │   ├── omnipod/
│   │   │   ├── client.py
│   │   │   └── adapter.py
│   │   └── fhir/
│   │       └── client.py
│   ├── db/
│   │   ├── __init__.py
│   │   ├── session.py              # Database session
│   │   ├── base.py                 # Base model
│   │   └── migrations/             # Alembic migrations
│   ├── tasks/
│   │   ├── __init__.py
│   │   ├── celery_app.py
│   │   ├── ml_tasks.py
│   │   └── notification_tasks.py
│   └── utils/
│       ├── __init__.py
│       ├── time_utils.py
│       └── validators.py
├── tests/
│   ├── unit/
│   ├── integration/
│   └── e2e/
├── scripts/
│   ├── seed_data.py
│   └── migrate.py
├── alembic.ini
├── requirements.txt
├── pyproject.toml
└── README.md
```

### 1.2 Core Service Implementations

#### 1.2.1 FastAPI Application Setup

```python
# src/main.py
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from contextlib import asynccontextmanager

from src.api.routes import auth, users, devices, data, predictions, alerts
from src.core.config import settings
from src.core.logging import setup_logging
from src.db.session import engine
from src.models.base import Base

@asynccontextmanager
async def lifespan(app: FastAPI):
    # Startup
    setup_logging()
    # Create tables (in production, use Alembic)
    async with engine.begin() as conn:
        await conn.run_sync(Base.metadata.create_all)
    yield
    # Shutdown
    await engine.dispose()

app = FastAPI(
    title="DiabetesCare Loop API",
    version="1.0.0",
    lifespan=lifespan,
    docs_url="/api/docs" if settings.ENVIRONMENT == "development" else None
)

# CORS
app.add_middleware(
    CORSMiddleware,
    allow_origins=settings.ALLOWED_ORIGINS,
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

# Routes
app.include_router(auth.router, prefix="/api/v1/auth", tags=["auth"])
app.include_router(users.router, prefix="/api/v1/users", tags=["users"])
app.include_router(devices.router, prefix="/api/v1/devices", tags=["devices"])
app.include_router(data.router, prefix="/api/v1/data", tags=["data"])
app.include_router(predictions.router, prefix="/api/v1/predictions", tags=["predictions"])
app.include_router(alerts.router, prefix="/api/v1/alerts", tags=["alerts"])

@app.get("/health")
async def health_check():
    return {"status": "healthy"}
```

#### 1.2.2 Configuration Management

```python
# src/core/config.py
from pydantic_settings import BaseSettings
from typing import List

class Settings(BaseSettings):
    # App
    APP_NAME: str = "DiabetesCare Loop"
    ENVIRONMENT: str = "development"
    DEBUG: bool = False

    # API
    API_V1_PREFIX: str = "/api/v1"
    ALLOWED_ORIGINS: List[str] = ["*"]

    # Database
    DATABASE_URL: str
    DATABASE_POOL_SIZE: int = 20
    DATABASE_MAX_OVERFLOW: int = 40

    # Redis
    REDIS_URL: str

    # Security
    SECRET_KEY: str
    ALGORITHM: str = "HS256"
    ACCESS_TOKEN_EXPIRE_MINUTES: int = 15
    REFRESH_TOKEN_EXPIRE_DAYS: int = 30

    # AWS
    AWS_ACCESS_KEY_ID: str
    AWS_SECRET_ACCESS_KEY: str
    AWS_REGION: str = "us-east-1"
    S3_BUCKET: str

    # ML
    ML_MODEL_PATH: str = "/models"
    ML_BATCH_SIZE: int = 32

    # Monitoring
    SENTRY_DSN: str | None = None
    LOG_LEVEL: str = "INFO"

    class Config:
        env_file = ".env"
        case_sensitive = True

settings = Settings()
```

#### 1.2.3 Database Models

```python
# src/models/user.py
from sqlalchemy import Column, String, DateTime, Boolean, Integer
from sqlalchemy.orm import relationship
from sqlalchemy.dialects.postgresql import UUID
import uuid
from datetime import datetime

from src.db.base import Base

class User(Base):
    __tablename__ = "users"

    id = Column(UUID(as_uuid=True), primary_key=True, default=uuid.uuid4)
    email = Column(String, unique=True, index=True, nullable=False)
    hashed_password = Column(String, nullable=False)
    full_name = Column(String)
    date_of_birth = Column(DateTime)
    diagnosis_date = Column(DateTime)

    # Settings
    target_glucose_min = Column(Integer, default=100)  # mg/dL
    target_glucose_max = Column(Integer, default=120)
    insulin_sensitivity_factor = Column(Integer, default=50)  # ISF
    carb_ratio = Column(Integer, default=10)  # ICR (1U per X grams)
    basal_rate = Column(Integer, default=1000)  # mU/hr (1.0 U/hr)

    # Status
    is_active = Column(Boolean, default=True)
    is_verified = Column(Boolean, default=False)
    created_at = Column(DateTime, default=datetime.utcnow)
    updated_at = Column(DateTime, default=datetime.utcnow, onupdate=datetime.utcnow)

    # Relationships
    devices = relationship("Device", back_populates="user")
    glucose_readings = relationship("GlucoseReading", back_populates="user")
    insulin_deliveries = relationship("InsulinDelivery", back_populates="user")
```

```python
# src/models/glucose_reading.py
from sqlalchemy import Column, Integer, DateTime, ForeignKey, Enum
from sqlalchemy.orm import relationship
from sqlalchemy.dialects.postgresql import UUID
import enum

from src.db.base import Base

class TrendArrow(str, enum.Enum):
    DOUBLE_UP = "DOUBLE_UP"           # ↑↑ (rising rapidly >3 mg/dL/min)
    SINGLE_UP = "SINGLE_UP"           # ↑ (rising 2-3 mg/dL/min)
    FORTY_FIVE_UP = "FORTY_FIVE_UP"   # ↗ (rising 1-2 mg/dL/min)
    FLAT = "FLAT"                     # → (stable ±1 mg/dL/min)
    FORTY_FIVE_DOWN = "FORTY_FIVE_DOWN" # ↘ (falling 1-2 mg/dL/min)
    SINGLE_DOWN = "SINGLE_DOWN"       # ↓ (falling 2-3 mg/dL/min)
    DOUBLE_DOWN = "DOUBLE_DOWN"       # ↓↓ (falling rapidly >3 mg/dL/min)

class GlucoseReading(Base):
    __tablename__ = "glucose_readings"

    id = Column(UUID(as_uuid=True), primary_key=True, default=uuid.uuid4)
    user_id = Column(UUID(as_uuid=True), ForeignKey("users.id"), nullable=False)
    device_id = Column(UUID(as_uuid=True), ForeignKey("devices.id"), nullable=False)

    # Glucose data
    value = Column(Integer, nullable=False)  # mg/dL
    trend_arrow = Column(Enum(TrendArrow))
    rate_of_change = Column(Integer)  # mg/dL per minute

    # Metadata
    timestamp = Column(DateTime, nullable=False, index=True)
    received_at = Column(DateTime, default=datetime.utcnow)

    # Relationships
    user = relationship("User", back_populates="glucose_readings")
    device = relationship("Device", back_populates="glucose_readings")

    # Index for efficient time-range queries
    __table_args__ = (
        Index('idx_user_timestamp', 'user_id', 'timestamp'),
    )
```

#### 1.2.4 Pydantic Schemas

```python
# src/schemas/glucose.py
from pydantic import BaseModel, Field, validator
from datetime import datetime
from uuid import UUID
from typing import Optional

from src.models.glucose_reading import TrendArrow

class GlucoseReadingCreate(BaseModel):
    value: int = Field(..., ge=20, le=600, description="Glucose in mg/dL")
    trend_arrow: Optional[TrendArrow] = None
    rate_of_change: Optional[int] = Field(None, ge=-10, le=10)
    timestamp: datetime
    device_id: UUID

    @validator('value')
    def validate_glucose_range(cls, v):
        if v < 40 or v > 400:
            # Log critical glucose values
            pass
        return v

class GlucoseReadingResponse(BaseModel):
    id: UUID
    user_id: UUID
    value: int
    trend_arrow: Optional[TrendArrow]
    timestamp: datetime

    class Config:
        from_attributes = True

class GlucoseHistoryRequest(BaseModel):
    start_time: datetime
    end_time: datetime

    @validator('end_time')
    def validate_time_range(cls, v, values):
        if 'start_time' in values and v < values['start_time']:
            raise ValueError('end_time must be after start_time')
        return v
```

#### 1.2.5 Service Layer

```python
# src/services/glucose_service.py
from sqlalchemy.ext.asyncio import AsyncSession
from sqlalchemy import select, and_
from datetime import datetime, timedelta
from typing import List, Optional

from src.models.glucose_reading import GlucoseReading
from src.schemas.glucose import GlucoseReadingCreate
from src.services.alert_service import AlertService

class GlucoseService:
    def __init__(self, db: AsyncSession):
        self.db = db
        self.alert_service = AlertService(db)

    async def create_reading(
        self,
        user_id: UUID,
        reading: GlucoseReadingCreate
    ) -> GlucoseReading:
        """
        Store glucose reading and trigger alerts if needed
        """
        db_reading = GlucoseReading(
            user_id=user_id,
            device_id=reading.device_id,
            value=reading.value,
            trend_arrow=reading.trend_arrow,
            rate_of_change=reading.rate_of_change,
            timestamp=reading.timestamp
        )

        self.db.add(db_reading)
        await self.db.commit()
        await self.db.refresh(db_reading)

        # Trigger alert checking (async task)
        await self.alert_service.check_glucose_alerts(user_id, db_reading)

        return db_reading

    async def get_recent_readings(
        self,
        user_id: UUID,
        hours: int = 3
    ) -> List[GlucoseReading]:
        """
        Get recent glucose readings for algorithm context
        """
        cutoff_time = datetime.utcnow() - timedelta(hours=hours)

        stmt = select(GlucoseReading).where(
            and_(
                GlucoseReading.user_id == user_id,
                GlucoseReading.timestamp >= cutoff_time
            )
        ).order_by(GlucoseReading.timestamp.desc())

        result = await self.db.execute(stmt)
        return result.scalars().all()

    async def get_statistics(
        self,
        user_id: UUID,
        start_time: datetime,
        end_time: datetime
    ) -> dict:
        """
        Calculate glycemic control statistics
        """
        readings = await self.get_readings_in_range(user_id, start_time, end_time)

        if not readings:
            return {}

        values = [r.value for r in readings]
        total_readings = len(values)

        # Time in Range (TIR)
        in_range = sum(1 for v in values if 70 <= v <= 180)
        below_range = sum(1 for v in values if v < 70)
        above_range = sum(1 for v in values if v > 180)

        return {
            "mean_glucose": sum(values) / total_readings,
            "std_glucose": statistics.stdev(values) if total_readings > 1 else 0,
            "coefficient_of_variation": (statistics.stdev(values) / (sum(values) / total_readings)) * 100,
            "time_in_range_percent": (in_range / total_readings) * 100,
            "time_below_range_percent": (below_range / total_readings) * 100,
            "time_above_range_percent": (above_range / total_readings) * 100,
            "estimated_hba1c": (sum(values) / total_readings + 46.7) / 28.7  # ADAG formula
        }
```

#### 1.2.6 Algorithm Service (Core Closed-Loop Logic)

```python
# src/services/algorithm_service.py
from typing import Tuple, Optional
import numpy as np
from datetime import datetime, timedelta

from src.ml.inference.predictor import GlucosePredictor
from src.services.glucose_service import GlucoseService
from src.models.user import User

class AlgorithmService:
    """
    Core closed-loop algorithm service
    Implements Model Predictive Control (MPC) for insulin dosing
    """

    def __init__(self, db: AsyncSession):
        self.db = db
        self.glucose_service = GlucoseService(db)
        self.predictor = GlucosePredictor()

        # Safety parameters
        self.MAX_BASAL_RATE = 5.0  # U/hr
        self.MIN_BASAL_RATE = 0.0
        self.MAX_BOLUS = 10.0  # U
        self.MAX_IOB = 15.0  # U

    async def calculate_insulin_adjustment(
        self,
        user_id: UUID
    ) -> Tuple[float, dict]:
        """
        Main algorithm: calculate insulin adjustment

        Returns:
            (basal_rate, metadata)
        """
        # 1. Get user settings
        user = await self._get_user(user_id)

        # 2. Get recent glucose data
        recent_readings = await self.glucose_service.get_recent_readings(
            user_id, hours=3
        )

        if len(recent_readings) < 3:
            # Not enough data, use programmed basal
            return user.basal_rate / 1000.0, {"reason": "insufficient_data"}

        # 3. Calculate current IOB
        current_iob = await self._calculate_iob(user_id)

        # 4. Predict future glucose (30 min and 60 min)
        current_glucose = recent_readings[0].value
        glucose_30min, glucose_60min = await self._predict_glucose(
            user_id, recent_readings
        )

        # 5. Calculate insulin adjustment using MPC
        target_glucose = (user.target_glucose_min + user.target_glucose_max) / 2

        basal_rate = await self._mpc_controller(
            current_glucose=current_glucose,
            predicted_glucose_30=glucose_30min,
            predicted_glucose_60=glucose_60min,
            target_glucose=target_glucose,
            current_iob=current_iob,
            user=user
        )

        # 6. Apply safety constraints
        basal_rate = self._apply_safety_limits(basal_rate, current_iob, user)

        metadata = {
            "current_glucose": current_glucose,
            "predicted_30min": glucose_30min,
            "predicted_60min": glucose_60min,
            "current_iob": current_iob,
            "target_glucose": target_glucose,
            "algorithm": "MPC",
            "timestamp": datetime.utcnow()
        }

        return basal_rate, metadata

    async def _mpc_controller(
        self,
        current_glucose: float,
        predicted_glucose_30: float,
        predicted_glucose_60: float,
        target_glucose: float,
        current_iob: float,
        user: User
    ) -> float:
        """
        Model Predictive Control algorithm

        Simplified version - in production, use advanced MPC with:
        - Longer prediction horizon
        - Insulin action curves
        - Meal absorption modeling
        - Exercise impact
        """
        # Glucose error
        error_now = current_glucose - target_glucose
        error_30 = predicted_glucose_30 - target_glucose
        error_60 = predicted_glucose_60 - target_glucose

        # Weighted error (prioritize near-term predictions)
        weighted_error = (error_now * 0.5) + (error_30 * 0.3) + (error_60 * 0.2)

        # Proportional control
        isf = user.insulin_sensitivity_factor
        basal_adjustment = -weighted_error / isf  # Negative because high glucose needs more insulin

        # Base basal rate
        base_basal = user.basal_rate / 1000.0  # Convert mU/hr to U/hr

        # New basal rate
        new_basal = base_basal + basal_adjustment

        # Conservative adjustments (max 50% change per 5 min cycle)
        max_change = base_basal * 0.5
        if abs(new_basal - base_basal) > max_change:
            new_basal = base_basal + (max_change if new_basal > base_basal else -max_change)

        return new_basal

    def _apply_safety_limits(
        self,
        basal_rate: float,
        current_iob: float,
        user: User
    ) -> float:
        """
        Multi-layered safety constraints
        """
        # Layer 1: Absolute limits
        basal_rate = max(self.MIN_BASAL_RATE, min(basal_rate, self.MAX_BASAL_RATE))

        # Layer 2: IOB limit (prevent insulin stacking)
        if current_iob > self.MAX_IOB:
            basal_rate = min(basal_rate, user.basal_rate / 1000.0 * 0.5)

        # Layer 3: User-specific max basal
        user_max_basal = user.basal_rate / 1000.0 * 3.0  # Max 3x programmed basal
        basal_rate = min(basal_rate, user_max_basal)

        return basal_rate

    async def _predict_glucose(
        self,
        user_id: UUID,
        recent_readings: List[GlucoseReading]
    ) -> Tuple[float, float]:
        """
        ML-based glucose prediction
        """
        # Prepare features
        features = self._extract_features(recent_readings)

        # Run ML model
        predictions = await self.predictor.predict(features)

        return predictions['30min'], predictions['60min']

    async def _calculate_iob(self, user_id: UUID) -> float:
        """
        Calculate Insulin On Board using exponential decay curve
        """
        # Get insulin deliveries from last 6 hours
        cutoff_time = datetime.utcnow() - timedelta(hours=6)

        deliveries = await self._get_insulin_deliveries(user_id, cutoff_time)

        iob = 0.0
        insulin_duration = 6.0  # hours (typical rapid-acting insulin)

        for delivery in deliveries:
            time_since = (datetime.utcnow() - delivery.timestamp).total_seconds() / 3600

            # Exponential decay curve (simplified)
            # In production, use validated insulin action curves
            if time_since < insulin_duration:
                remaining_fraction = 1 - (time_since / insulin_duration) ** 2
                iob += delivery.units * remaining_fraction

        return iob
```

---

## 2. Mobile Application Design

### 2.1 iOS App Architecture (Swift + SwiftUI)

```
iOS App/
├── DiabetesCareApp.swift           # App entry point
├── Core/
│   ├── Network/
│   │   ├── APIClient.swift         # Network layer
│   │   ├── Endpoints.swift
│   │   └── NetworkError.swift
│   ├── Bluetooth/
│   │   ├── BluetoothManager.swift
│   │   ├── CGMManager.swift
│   │   └── PumpManager.swift
│   ├── Storage/
│   │   ├── CoreDataStack.swift
│   │   ├── Models.xcdatamodeld
│   │   └── UserDefaultsManager.swift
│   ├── ML/
│   │   ├── MLModelManager.swift
│   │   └── OnDevicePredictor.swift
│   └── Security/
│       ├── KeychainManager.swift
│       └── Encryption.swift
├── Features/
│   ├── Dashboard/
│   │   ├── DashboardView.swift
│   │   ├── DashboardViewModel.swift
│   │   ├── GlucoseGraphView.swift
│   │   └── StatusCardView.swift
│   ├── Devices/
│   │   ├── DevicesView.swift
│   │   ├── DevicePairingView.swift
│   │   └── DeviceViewModel.swift
│   ├── Logging/
│   │   ├── MealLogView.swift
│   │   ├── ExerciseLogView.swift
│   │   └── LoggingViewModel.swift
│   ├── Settings/
│   │   ├── SettingsView.swift
│   │   └── SettingsViewModel.swift
│   └── Alerts/
│       ├── AlertsView.swift
│       └── AlertManager.swift
├── Models/
│   ├── User.swift
│   ├── GlucoseReading.swift
│   ├── InsulinDelivery.swift
│   └── Device.swift
├── Services/
│   ├── AuthService.swift
│   ├── DataSyncService.swift
│   ├── AlgorithmService.swift
│   └── NotificationService.swift
└── Utils/
    ├── Extensions/
    ├── Constants.swift
    └── Helpers.swift
```

### 2.2 Key iOS Components

#### 2.2.1 Bluetooth Manager (CGM Communication)

```swift
// Core/Bluetooth/CGMManager.swift
import CoreBluetooth
import Combine

class CGMManager: NSObject, ObservableObject {
    @Published var currentGlucose: GlucoseReading?
    @Published var connectionStatus: ConnectionStatus = .disconnected

    private var centralManager: CBCentralManager!
    private var cgmPeripheral: CBPeripheral?

    // Dexcom G7 Service UUID
    private let cgmServiceUUID = CBUUID(string: "F8083532-849E-531C-C594-30F1F86A4EA5")
    private let glucoseCharacteristicUUID = CBUUID(string: "F8083533-849E-531C-C594-30F1F86A4EA5")

    override init() {
        super.init()
        centralManager = CBCentralManager(delegate: self, queue: nil)
    }

    func startScanning() {
        centralManager.scanForPeripherals(
            withServices: [cgmServiceUUID],
            options: [CBCentralManagerScanOptionAllowDuplicatesKey: false]
        )
    }

    func connect(to peripheral: CBPeripheral) {
        cgmPeripheral = peripheral
        cgmPeripheral?.delegate = self
        centralManager.connect(peripheral, options: nil)
    }
}

extension CGMManager: CBCentralManagerDelegate {
    func centralManagerDidUpdateState(_ central: CBCentralManager) {
        if central.state == .poweredOn {
            startScanning()
        }
    }

    func centralManager(_ central: CBCentralManager, didDiscover peripheral: CBPeripheral, advertisementData: [String : Any], rssi RSSI: NSNumber) {
        // Auto-connect if previously paired
        if UserDefaults.standard.string(forKey: "pairedCGMUUID") == peripheral.identifier.uuidString {
            connect(to: peripheral)
        }
    }

    func centralManager(_ central: CBCentralManager, didConnect peripheral: CBPeripheral) {
        connectionStatus = .connected
        peripheral.discoverServices([cgmServiceUUID])
    }
}

extension CGMManager: CBPeripheralDelegate {
    func peripheral(_ peripheral: CBPeripheral, didDiscoverServices error: Error?) {
        guard let services = peripheral.services else { return }

        for service in services {
            peripheral.discoverCharacteristics([glucoseCharacteristicUUID], for: service)
        }
    }

    func peripheral(_ peripheral: CBPeripheral, didDiscoverCharacteristicsFor service: CBService, error: Error?) {
        guard let characteristics = service.characteristics else { return }

        for characteristic in characteristics {
            if characteristic.uuid == glucoseCharacteristicUUID {
                // Subscribe to glucose notifications
                peripheral.setNotifyValue(true, for: characteristic)
            }
        }
    }

    func peripheral(_ peripheral: CBPeripheral, didUpdateValueFor characteristic: CBCharacteristic, error: Error?) {
        if characteristic.uuid == glucoseCharacteristicUUID {
            if let data = characteristic.value {
                let glucose = parseGlucoseData(data)
                DispatchQueue.main.async {
                    self.currentGlucose = glucose
                }
                // Send to backend
                Task {
                    await uploadGlucoseReading(glucose)
                }
            }
        }
    }

    private func parseGlucoseData(_ data: Data) -> GlucoseReading {
        // Parse Dexcom data format
        // Actual implementation depends on manufacturer protocol
        let bytes = [UInt8](data)
        let glucoseValue = Int(bytes[0]) | (Int(bytes[1]) << 8)
        let trendArrow = TrendArrow(rawValue: bytes[2]) ?? .flat

        return GlucoseReading(
            value: glucoseValue,
            trendArrow: trendArrow,
            timestamp: Date()
        )
    }
}
```

#### 2.2.2 Dashboard View (SwiftUI)

```swift
// Features/Dashboard/DashboardView.swift
import SwiftUI
import Charts

struct DashboardView: View {
    @StateObject private var viewModel = DashboardViewModel()

    var body: some View {
        ScrollView {
            VStack(spacing: 20) {
                // Current Glucose Card
                CurrentGlucoseCard(
                    glucose: viewModel.currentGlucose,
                    trendArrow: viewModel.trendArrow,
                    delta: viewModel.glucoseDelta
                )

                // Glucose Graph (8 hours)
                GlucoseGraphView(readings: viewModel.recentReadings)
                    .frame(height: 250)

                // System Status
                SystemStatusView(
                    loopStatus: viewModel.loopStatus,
                    pumpStatus: viewModel.pumpStatus,
                    cgmStatus: viewModel.cgmStatus,
                    iob: viewModel.currentIOB
                )

                // Quick Actions
                QuickActionsView(
                    onLogMeal: { viewModel.showMealLog() },
                    onLogExercise: { viewModel.showExerciseLog() }
                )

                // Predictions
                PredictionCardView(
                    prediction30: viewModel.prediction30min,
                    prediction60: viewModel.prediction60min
                )
            }
            .padding()
        }
        .navigationTitle("Dashboard")
        .onAppear {
            viewModel.startMonitoring()
        }
    }
}

struct CurrentGlucoseCard: View {
    let glucose: Int?
    let trendArrow: TrendArrow?
    let delta: Int?

    var body: some View {
        VStack {
            HStack(alignment: .center) {
                Text(glucose.map { "\($0)" } ?? "---")
                    .font(.system(size: 72, weight: .bold))
                    .foregroundColor(glucoseColor)

                if let arrow = trendArrow {
                    Text(arrow.symbol)
                        .font(.system(size: 48))
                }
            }

            Text("mg/dL")
                .font(.headline)
                .foregroundColor(.secondary)

            if let delta = delta {
                Text("\(delta > 0 ? "+" : "")\(delta) mg/dL")
                    .font(.subheadline)
                    .foregroundColor(.secondary)
            }
        }
        .frame(maxWidth: .infinity)
        .padding()
        .background(Color(.systemBackground))
        .cornerRadius(12)
        .shadow(radius: 2)
    }

    var glucoseColor: Color {
        guard let glucose = glucose else { return .gray }

        if glucose < 70 {
            return .red
        } else if glucose > 180 {
            return .orange
        } else {
            return .green
        }
    }
}
```

---

## 3. Database Schema Design

### 3.1 TimescaleDB Schema (Time-Series Data)

```sql
-- Hypertable for glucose readings
CREATE TABLE glucose_readings (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id),
    device_id UUID NOT NULL REFERENCES devices(id),
    value INTEGER NOT NULL CHECK (value >= 20 AND value <= 600),
    trend_arrow VARCHAR(20),
    rate_of_change INTEGER,
    timestamp TIMESTAMPTZ NOT NULL,
    received_at TIMESTAMPTZ DEFAULT NOW(),
    CONSTRAINT glucose_reading_unique UNIQUE (user_id, timestamp)
);

-- Convert to hypertable (TimescaleDB)
SELECT create_hypertable('glucose_readings', 'timestamp');

-- Indexes
CREATE INDEX idx_glucose_user_time ON glucose_readings (user_id, timestamp DESC);
CREATE INDEX idx_glucose_device ON glucose_readings (device_id);

-- Continuous aggregates for statistics (TimescaleDB feature)
CREATE MATERIALIZED VIEW glucose_hourly_stats
WITH (timescaledb.continuous) AS
SELECT
    user_id,
    time_bucket('1 hour', timestamp) AS bucket,
    AVG(value) as avg_glucose,
    STDDEV(value) as std_glucose,
    MIN(value) as min_glucose,
    MAX(value) as max_glucose,
    COUNT(*) as reading_count
FROM glucose_readings
GROUP BY user_id, bucket;

-- Retention policy (auto-delete data >2 years)
SELECT add_retention_policy('glucose_readings', INTERVAL '2 years');
```

### 3.2 Complete Database Schema

```sql
-- Users table
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) UNIQUE NOT NULL,
    hashed_password VARCHAR(255) NOT NULL,
    full_name VARCHAR(255),
    date_of_birth TIMESTAMPTZ,
    diagnosis_date TIMESTAMPTZ,

    -- Diabetes settings
    target_glucose_min INTEGER DEFAULT 100,
    target_glucose_max INTEGER DEFAULT 120,
    insulin_sensitivity_factor INTEGER DEFAULT 50,
    carb_ratio INTEGER DEFAULT 10,
    basal_rate INTEGER DEFAULT 1000,  -- mU/hr

    -- Account status
    is_active BOOLEAN DEFAULT TRUE,
    is_verified BOOLEAN DEFAULT FALSE,

    -- Timestamps
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Devices table
CREATE TABLE devices (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    device_type VARCHAR(20) NOT NULL CHECK (device_type IN ('CGM', 'PUMP')),
    manufacturer VARCHAR(50) NOT NULL,
    model VARCHAR(50) NOT NULL,
    serial_number VARCHAR(100),

    -- Connection
    bluetooth_id VARCHAR(100),
    last_connected_at TIMESTAMPTZ,
    is_active BOOLEAN DEFAULT TRUE,

    -- Metadata
    firmware_version VARCHAR(20),
    battery_level INTEGER,

    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Insulin deliveries table
CREATE TABLE insulin_deliveries (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id),
    device_id UUID NOT NULL REFERENCES devices(id),

    delivery_type VARCHAR(20) NOT NULL CHECK (delivery_type IN ('BASAL', 'BOLUS', 'CORRECTION')),
    units INTEGER NOT NULL,  -- milliUnits (mU)
    duration INTEGER,  -- minutes (for extended bolus)

    -- Context
    is_automatic BOOLEAN DEFAULT FALSE,  -- Closed-loop vs manual
    algorithm_version VARCHAR(20),

    timestamp TIMESTAMPTZ NOT NULL,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

SELECT create_hypertable('insulin_deliveries', 'timestamp');
CREATE INDEX idx_insulin_user_time ON insulin_deliveries (user_id, timestamp DESC);

-- Meals table
CREATE TABLE meals (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id),

    carbohydrates INTEGER NOT NULL,  -- grams
    protein INTEGER,  -- grams
    fat INTEGER,  -- grams

    meal_type VARCHAR(20),  -- breakfast, lunch, dinner, snack
    photo_url VARCHAR(500),
    notes TEXT,

    timestamp TIMESTAMPTZ NOT NULL,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_meals_user_time ON meals (user_id, timestamp DESC);

-- Activities table (exercise)
CREATE TABLE activities (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id),

    activity_type VARCHAR(50),  -- running, walking, cycling, etc.
    intensity VARCHAR(20),  -- low, moderate, high
    duration INTEGER NOT NULL,  -- minutes

    start_time TIMESTAMPTZ NOT NULL,
    end_time TIMESTAMPTZ,

    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Alerts table
CREATE TABLE alerts (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id),

    alert_type VARCHAR(50) NOT NULL,  -- high_glucose, low_glucose, predicted_low, etc.
    severity VARCHAR(20) NOT NULL,  -- info, warning, critical

    glucose_value INTEGER,
    predicted_glucose INTEGER,

    message TEXT NOT NULL,
    is_acknowledged BOOLEAN DEFAULT FALSE,
    acknowledged_at TIMESTAMPTZ,

    timestamp TIMESTAMPTZ NOT NULL,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

SELECT create_hypertable('alerts', 'timestamp');
CREATE INDEX idx_alerts_user_time ON alerts (user_id, timestamp DESC);
CREATE INDEX idx_alerts_unacknowledged ON alerts (user_id, is_acknowledged, timestamp DESC);
```

---

## 4. API Design

### 4.1 RESTful API Endpoints

See Appendix B in System Architecture document for full endpoint list.

### 4.2 WebSocket Protocol (Real-Time Updates)

```python
# WebSocket connection for real-time glucose updates
from fastapi import WebSocket, WebSocketDisconnect

class ConnectionManager:
    def __init__(self):
        self.active_connections: Dict[UUID, List[WebSocket]] = {}

    async def connect(self, websocket: WebSocket, user_id: UUID):
        await websocket.accept()
        if user_id not in self.active_connections:
            self.active_connections[user_id] = []
        self.active_connections[user_id].append(websocket)

    def disconnect(self, websocket: WebSocket, user_id: UUID):
        self.active_connections[user_id].remove(websocket)

    async def send_glucose_update(self, user_id: UUID, data: dict):
        if user_id in self.active_connections:
            for connection in self.active_connections[user_id]:
                await connection.send_json(data)

manager = ConnectionManager()

@app.websocket("/ws/{user_id}")
async def websocket_endpoint(websocket: WebSocket, user_id: UUID):
    await manager.connect(websocket, user_id)
    try:
        while True:
            # Keep connection alive
            await websocket.receive_text()
    except WebSocketDisconnect:
        manager.disconnect(websocket, user_id)
```

---

## 5. Device Communication Protocol

### 5.1 Bluetooth Protocol Flow

```
Mobile App <-> CGM Device
1. Scan for devices (filter by service UUID)
2. Connect to discovered device
3. Pair and authenticate
4. Subscribe to glucose characteristic notifications
5. Receive glucose readings (pushed by device every 5 min)
6. Handle disconnections and reconnections gracefully

Mobile App <-> Insulin Pump
1. Scan and connect
2. Authenticate (pump-specific protocol)
3. Send insulin commands (requires confirmation)
4. Receive pump status updates
5. Monitor for occlusions, low reservoir, etc.
```

### 5.2 Safety Protocol

```
Insulin Command Safety Checks:
1. Mobile app validates command
2. Mobile app sends to backend for validation (if online)
3. Backend runs safety checks:
   - Within max basal rate?
   - Would exceed max IOB?
   - User alert settings respected?
4. Backend approves or rejects
5. Mobile app sends to pump
6. Pump firmware validates
7. Pump executes
8. Pump sends confirmation
9. Mobile app + backend record execution
```

---

## 6. Algorithm Implementation

### 6.1 Glucose Prediction Model

**Approach:** LSTM (Long Short-Term Memory) neural network

**Inputs:**
- Last 3 hours of glucose readings (36 values at 5-min intervals)
- Rate of change
- Insulin on board (IOB)
- Active carbs (COB)
- Recent meals (carbs, timing)
- Recent exercise (type, duration, timing)
- Time of day (circadian rhythm)
- Day of week

**Outputs:**
- Predicted glucose at 30 minutes
- Predicted glucose at 60 minutes
- Confidence intervals (95%)

**Training:**
- Dataset: Clinical trial data + real-world data
- Personalization: Fine-tune on user's historical data
- Validation: Hold-out test set with error metrics

### 6.2 Insulin Dosing Algorithm

**Model Predictive Control (MPC):**
1. Predict future glucose trajectory
2. Optimize insulin delivery to minimize cost function:
   - Cost = w1 * (glucose_error^2) + w2 * (insulin_usage^2)
3. Apply safety constraints
4. Execute first step of optimal plan
5. Repeat every 5 minutes

---

## 7. Testing Strategy

See dedicated Testing & Validation Strategy document.

---

**Document Approval:**
- Technical Lead: _________________
- Backend Architect: _________________
- Mobile Lead (iOS): _________________
- Mobile Lead (Android): _________________

**Change Log:**
| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2025-11-23 | Initial | First draft |
