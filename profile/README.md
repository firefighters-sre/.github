# Firefighters SRE

This architecture is designed to simulate a building management and monitoring system. Various microservices are tasked with monitoring and managing specific aspects, such as people access, mobility (utilization of stairs and elevators), environment, and building security. The setup draws an analogy to a Site Reliability Engineering (SRE) environment, where multiple components collaborate to ensure safety, efficiency, and reliability.

## Technology Stack

- **Microservices Framework**: Quarkus
- **Messaging Platform**: AMQ Streams (Kafka) and Red Hat Fuse (Apache Camel)
- **Database**: PostgreSQL (for this example, other databases can be integrated)
- **Deployment**: OpenShift (Kubernetes with Helm charts)
- **Monitoring & Tracing**: Prometheus, Jaeger, and Grafana

## Microservices

1. 🛎️ [**Access Microservice (Concierge-App)**](https://github.com/firefighters-sre/concierge-app): Manages the entrance and exit of individuals from the building, ensuring a streamlined flow and security.
2. 🚶‍♂️🔝 [**Mobility Microservice (Mobility-App)**](https://github.com/firefighters-sre/mobility-app): Monitors and manages the utilization of stairs and elevators, promoting safety and efficient vertical movement within the building.
3. 🏠 [**Building Microservice (Building-App)**](https://github.com/firefighters-sre/building-app): Handles information regarding the building, such as temperature, air quality, and floor occupancy. 
4. 🛡️ **Security Microservice**: Focuses on the overall security of the building, integrating with cameras, alarms, and other security systems. (Further details to be provided)

## Kafka Topics

The system leverages Kafka for messaging between microservices. Below are the primary topics utilized:

### 1. Lobby

This topic collects events related to activities in the building's lobby, such as the entrance of individuals.

- **Producing Microservice**: External systems or devices.
- **Consuming Microservice**: Access Microservice (Concierge-App).
- **Output to**: Entrance Topic.

### 2. Entrance

This topic handles events post-processing from the Lobby, primarily marking individuals' entrance into the building.

- **Producing Microservice**: Access Microservice (Concierge-App).
- **Consuming Microservice**: Mobility Microservice (Mobility-App).
- **Output to**: Either Stairs or Elevator Topic.

### 3. Elevator

This topic captures events associated with elevator operations, including movement between floors, door actions, and any anomalies.

- **Producing Microservice**: Mobility Microservice (Mobility-App).
- **Consuming Microservice**: Building Microservice (Building-App).
- **Output to**: Database.

### 4. Stairs

This topic gathers data about the use of stairs, tracking the movement of individuals using the stairs.

- **Producing Microservice**: Mobility Microservice (Mobility-App).
- **Consuming Microservice**: Building Microservice (Building-App).
- **Output to**: Database.

## Database Structure

The architecture utilizes PostgreSQL as its primary database, managing multiple tables related to access, building floors, and environmental conditions.

### External Area Database

This database stores information related to the external area of the building, mainly focusing on tracking people's access to the building.

**Tables**:

- **Person**:
  - **id** (primary key): Unique identifier for each person.
  - **name**: The name of the person.
  - **type**: Categorizes the person as a visitor or employee.
  - **contact**: Contact details for the person (could be a phone number or email).

### SQL Script for External Area Database

```sql
CREATE DATABASE externaldb;

-- Switch to the created database
\c externaldb;

-- Create the AccessLog table
CREATE TABLE AccessLog (
    recordId SERIAL PRIMARY KEY,
    personId INT NOT NULL,
    entryTime TIMESTAMP NOT NULL,
    exitTime TIMESTAMP,
    personType VARCHAR(50) CHECK (personType IN ('visitor', 'employee')),
    destination VARCHAR(255)
);

-- Create the Person table
CREATE TABLE Person (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    type VARCHAR(50) CHECK (type IN ('visitor', 'employee')),
    contact VARCHAR(255)
);
```

### Building Database

This database focuses on the internal structure of the building, capturing details about individual floors, environmental conditions, and potential security threats.

**Tables**:

- **FloorData**:
  - **floor_number** (primary key): Denotes the specific floor within the building.
  - **people_count**: Tracks the current number of people on the floor.
  - **structure_quality**: Rates the structural quality on a scale from 1 to 5.
  - **max_people**: Sets a limit for the maximum number of people allowed on the floor.
  - **o2_level**: Measures the current oxygen level on the floor.
  - **co2_level**: Monitors the carbon dioxide level on the floor.

### SQL Script to Create the Database

```sql
CREATE DATABASE buildingdb;

-- Switch to the created database
\c buildingdb;

-- Create the AccessLog table
CREATE TABLE AccessLog (
    recordId SERIAL PRIMARY KEY,
    personId INT NOT NULL,
    entryTime TIMESTAMP NOT NULL,
    exitTime TIMESTAMP,
    personType VARCHAR(50) CHECK (personType IN ('visitor', 'employee')),
    destination VARCHAR(255)
);

-- Create the FloorData table
CREATE TABLE FloorData (
    floor_number INT PRIMARY KEY,
    people_count INT NOT NULL DEFAULT 0,
    structure_quality INT CHECK (structure_quality BETWEEN 1 AND 5) NOT NULL,
    max_people INT NOT NULL,
    o2_level DECIMAL NOT NULL,
    co2_level DECIMAL NOT NULL
);
```
