# Course Registration System Design

A system design for a university course registration platform including ranked course preferences, enrollment constraints, waitlists, and multi-day registration.

This project was developed as a hypothetical system design presentation for a university with approximately 40,000 students. The design focuses on API structure, data modeling, asynchronous processing, and scaling.

[View the full system design presentation](./Course-Registration-System-Design.pdf)

## Problem

Traditional first-come, first-served registration systems can create heavy traffic spikes and disadvantage students who cannot register immediately when enrollment opens.

This design explores a ranked registration model. Students submit up a list of preferred courses for each day before registration begins. The system processes one class per day over a five-day registration period, attempting to enroll each student while factoring in course capacity and credit limits.

After this ranked enrollment period, open enrollment allows students to make any additional changes directly.

## Registration Flow

1. Students may rank up to five preferred courses.
2. Students may also rank preferred lab or seminar sections.
3. The registration system processes one course preference per student each day.
4. The enrollment service validates registration constraints and attempts to reserve a seat.
5. Successful enrollments are written to the database.
6. Students who cannot be enrolled may be added to a waitlist.
7. Notification jobs are queued asynchronously for enrollment or waitlist updates.
   
## System Architecture

The design separates course information, student preferences, enrollment operations, and waitlist management into distinct services and APIs:

* **Course Catalog API** for course and section information
* **Course Preferences API** for ranked student selections
* **Ranked Enrollment API** for scheduled preference processing
* **Enrollment API** for enrollment and drop operations
* **Waitlist API** for waitlist membership and ordering
* **Administrative Scheduler** for triggering each registration wave
* **Background Workers** for notifications and other asynchronous work

## Design Considerations

### Concurrency

Popular courses may receive thousands of enrollment attempts at nearly the same time. Enrollment operations require capacity checks to prevent multiple students from claiming the same remaining seat.

### Database Consistency

Enrollment, course capacity, and waitlist state must remain consistent even if a request fails midway through processing. The design uses transactions to achieve this.

### Asynchronous Processing

Tasks that do not need to block enrollment, such as sending notifications, can be moved to background workers. This reduces the amount of work performed in the more important enrollment path.

### Caching

Frequently accessed course and catalog data are cached because they are read much more often than they are modified. The design considers what variables to cache and how this would improve performance.

### Scaling

The initial design targets a university with roughly 40,000 students, but the architecture also considers what changes would need to be made regarding expansion to a state/national level.

Scaling strategies include:

* Processing registration jobs across multiple servers
* Partitioning data by university or campus
* Separating what is mostly read-heavy course catalog traffic from any writes.
* Scaling individual services independently based on workload

## Presentation

The complete design was developed into a 30 to 45-minute technical project proposal covering:

* Requirements and assumptions
* APIs
* Data modeling
* Constraints
* Concurrency handling
* Asynchronous jobs
* Caching
* Failure points and security concerns
* Scaling from a single university to larger deployments
* Tradeoffs the design makes and why they make sense

## Project Scope

This repository is a system design case study. The goal was to work through the architectural decisions that go into building a large system at university scale and explainingthe reasoning behind those decisions.
