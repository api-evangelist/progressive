# Progressive Insurance GraphQL Schema

## Overview

Progressive Insurance is one of the largest auto insurers in the United States, offering personal and commercial auto, home, renters, motorcycle, boat, RV, and other insurance products. Progressive operates a developer portal at developer.progressive.com providing APIs for auto insurance quoting, certificate of insurance generation, and agent portal integrations.

This conceptual GraphQL schema models the core domains exposed through Progressive's partner and agent APIs, including policy lifecycle management, telematics via the Snapshot program, claims processing, and quoting.

## Schema Source

**Type:** Conceptual  
**Based on:** Progressive Developer Portal (developer.progressive.com), public product documentation, partner API surface area, and insurance industry domain knowledge.  
**Schema file:** progressive-schema.graphql

## Domains Covered

### Policies
Progressive's core policy types are modeled as distinct GraphQL object types sharing a base `Policy` type:

- `Policy` — base policy record with status, term, coverages, premium, and endorsements
- `AutoPolicy` — extends with vehicles, additional drivers, and Snapshot telematics enrollment
- `HomePolicy` — adds property address, construction details, and dwelling coverage limits
- `RentersPolicy` — personal property and liability coverage for renters
- `MotorcyclePolicy` — motorcycle-specific with rider experience
- `BoatPolicy` — vessel details, navigation area, harbor location
- `RVPolicy` — RV/motorhome coverage with full-timer flag
- `CommercialPolicy` — business fleet policies with employee count and multiple vehicles
- `PolicyDetails` — billing, mailing, payment schedule details
- `PolicyTerm` — term length, start and end dates
- `Endorsement` — optional policy add-ons and riders

### People & Insureds
- `PolicyHolder` — primary account holder
- `NamedInsured` — individuals listed on the policy
- `AdditionalDriver` — other household or authorized drivers with MVR linkage
- `Agent` — licensed agent with agency affiliation and contact details
- `Agency` — insurance agency entity

### Vehicles
- `Vehicle` — core vehicle record with VIN, make, model, year, usage, and mileage
- `VIN` — raw VIN string with decoded detail
- `VehicleInfo` — decoded VIN data (body style, engine, MSRP)
- `AutoMake` — manufacturer reference
- `AutoModel` — model reference tied to make
- `Vessel` — boat/watercraft with hull ID, length, horsepower

### Driving History & MVR
- `DriveHistory` — lifetime driving record for a driver
- `MVR` — motor vehicle report ordered from state DMV
- `MVREvent` — individual violation, accident, or conviction on the MVR
- `DriverRecord` — summary accident/violation counts and clean-record years
- `SafetyFeature` — vehicle safety technology (e.g., automatic emergency braking)
- `CrashPrevention` — crash avoidance system details

### Coverage & Premium
- `Coverage` — generic coverage with type, limits, and deductible
- `BodilyInjury` — per-person / per-occurrence BI limits
- `PropertyDamage` — PD liability limit
- `Collision` — collision coverage with deductible
- `Comprehensive` — comp coverage with deductible
- `UninsuredMotorist` — UM/UIM limits
- `MedicalPayments` — MedPay limit
- `PersonalInjuryProtection` — PIP limit and deductible
- `Deductible` — deductible amount and waiver flag
- `Premium` — total annual/term premium with monthly installment and per-coverage breakdown

### Quoting
- `RateQuote` — full quote record with quote number, expiration, coverages, and estimated premium
- `Discount` — applied or available discounts with type, percentage, and dollar amount
- `HomeOwner` — homeownership details used for multi-policy discount qualification
- `MultiPolicy` — multi-policy bundle discount
- `MultiCar` — multi-vehicle discount
- `GoodDriver` — accident/violation-free discount
- `SnapshotDiscount` — Snapshot program final discount after measurement period

### Snapshot Telematics
Progressive's Snapshot program uses telematics to score driving behavior and apply discounts.

- `Snapshot` — enrollment record tied to a vehicle
- `DriveScore` — composite score with sub-scores for braking, time-of-day, and mileage
- `TripData` — individual trip with distance, duration, and events
- `FuelEconomy` — MPG and fuel consumption per trip
- `IdleTime` — idle minutes per trip
- `HardBrake` — hard braking event with timestamp, speed, and severity
- `DriveEvent` — generic telematics event with type and GPS coordinates

### Claims
- `Claim` — claim record with policy linkage, loss date, adjuster, and repair shop
- `ClaimStatus` — current claim status with status code and timestamp
- `ClaimPayment` — individual payment issued against a claim
- `ClaimAdjuster` — assigned adjuster contact information
- `RepairShop` — repair facility with Progressive-certified flag
- `Estimate` — damage estimate with labor and parts breakdown
- `Rental` — rental car reimbursement details

### Auth & API Access
- `APIKey` — partner/agent API key with scopes and expiration
- `Token` — OAuth2 bearer token for API authentication

## Key Operations

### Queries
| Operation | Description |
|---|---|
| `policy(id)` | Fetch a single policy by ID |
| `policies(holderId, status, type)` | List policies for a holder |
| `autoPolicy(id)` | Fetch auto policy with vehicles and drivers |
| `rateQuote(quoteNumber)` | Retrieve a saved quote |
| `decodeVIN(vin)` | Decode a VIN to vehicle info |
| `claim(id)` | Fetch a single claim |
| `claims(policyId, status)` | List claims with optional filters |
| `snapshot(vehicleId)` | Get Snapshot enrollment for a vehicle |
| `driveScore(snapshotId)` | Get current Snapshot drive score |
| `agentLocator(zip, radiusMiles)` | Find agents near a ZIP code |
| `mvr(driverId)` | Retrieve motor vehicle report |

### Mutations
| Operation | Description |
|---|---|
| `createRateQuote(input)` | Generate a new insurance rate quote |
| `bindPolicy(quoteId, paymentMethod)` | Bind a quote into an active policy |
| `cancelPolicy(policyId, reason)` | Cancel an existing policy |
| `addVehicle(policyId, vehicleInput)` | Add a vehicle to an auto policy |
| `removeVehicle(policyId, vehicleId)` | Remove a vehicle from an auto policy |
| `addDriver(policyId, driverInput)` | Add a driver to an auto policy |
| `fileClaim(policyId, input)` | File a new insurance claim |
| `enrollSnapshot(vehicleId)` | Enroll a vehicle in Snapshot telematics |
| `unenrollSnapshot(snapshotId)` | Remove a vehicle from Snapshot |
| `generateAPIKey(name, scopes)` | Create a new API key for partner access |
| `revokeAPIKey(keyId)` | Revoke an existing API key |
| `requestToken(clientId, clientSecret)` | Obtain an OAuth2 access token |

### Subscriptions
| Operation | Description |
|---|---|
| `claimStatusChanged(claimId)` | Real-time claim status updates |
| `snapshotScoreUpdated(snapshotId)` | Drive score changes as trips are processed |
| `policyStatusChanged(policyId)` | Policy status change notifications |

## Types Summary

| Category | Types |
|---|---|
| Policies | Policy, AutoPolicy, HomePolicy, RentersPolicy, MotorcyclePolicy, BoatPolicy, RVPolicy, CommercialPolicy, PolicyDetails, PolicyTerm, Endorsement |
| People | PolicyHolder, NamedInsured, AdditionalDriver, Agent, Agency |
| Vehicles | Vehicle, VIN, VehicleInfo, AutoMake, AutoModel, Vessel |
| Driving History | DriveHistory, MVR, MVREvent, DriverRecord, SafetyFeature, CrashPrevention |
| Coverage | Coverage, CoverageLimits, BodilyInjury, PropertyDamage, Collision, Comprehensive, UninsuredMotorist, MedicalPayments, PersonalInjuryProtection, Deductible, Premium, PremiumBreakdown |
| Quoting | RateQuote, Discount, HomeOwner, MultiPolicy, MultiCar, GoodDriver, SnapshotDiscount |
| Snapshot | Snapshot, DriveScore, TripData, FuelEconomy, IdleTime, HardBrake, DriveEvent |
| Claims | Claim, ClaimStatus, ClaimPayment, ClaimAdjuster, RepairShop, Estimate, Rental |
| Auth | APIKey, Token |
| Shared | Address |
| Enums | PolicyStatus, PolicyType, ClaimStatusCode, PaymentFrequency, VehicleUse, DriveEventType, DiscountType, CoverageType |

**Total named types (objects + enums + scalars): 68**

## References

- Progressive Developer Portal: https://developer.progressive.com/s/
- Progressive Auto Quote API Docs: https://developer.progressive.com/s/clautoquoteapidoc
- Progressive Partners: https://www.progressive.com/partners/
- Progressive Website: https://www.progressive.com/
