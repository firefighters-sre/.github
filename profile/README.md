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
5. 🧑‍🤝‍🧑 [**People Microservice (People-App)**](https://github.com/firefighters-sre/people-app): Manages individuals' information and interactions within the building.

## Deployment and Monitoring

1. 🚀 [**Deployment Microservice (Helm-Charts)**](https://github.com/firefighters-sre/helm-charts): Contains the Helm charts for deploying the microservices on OpenShift.
2. 📊 [**Monitoring Microservice (Grafana-Dashboards)**](https://github.com/firefighters-sre/grafana-dashboards): Houses the Grafana dashboards for monitoring the system's performance.

## Documentation

1. 📚 [**Documentation Repository (Docs)**](https://github.com/firefighters-sre/docs): Contains all the necessary documentation for understanding and working with the system.
