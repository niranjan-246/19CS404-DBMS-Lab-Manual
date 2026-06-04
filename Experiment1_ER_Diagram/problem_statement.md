# ER Diagram Workshop – Submission Template

## Objective
To understand and apply ER modeling concepts by creating ER diagrams for real-world applications.

## Purpose
Gain hands-on experience in designing ER diagrams that represent database structure including entities, relationships, attributes, and constraints.

---

# Scenario A: City Fitness Club Management

*Business Context:*  
FlexiFit Gym wants a database to manage its members, trainers, and fitness programs.

*Requirements:*  
- Members register with name, membership type, and start date.  
- Each member can join multiple programs (Yoga, Zumba, Weight Training).  
- Trainers assigned to programs; a program may have multiple trainers.  
- Members may book personal training sessions with trainers.  
- Attendance recorded for each session.  
- Payments tracked for memberships and sessions.

### ER Diagram:

<img width="1536" height="1024" alt="City ER diagram" src="https://github.com/user-attachments/assets/3f8512a0-14e5-4213-82e6-454c5894fe5c" />


# City Library Event & Book Lending System

## Entities and Attributes

### 1. MEMBER
- MemberID (Primary Key)
- Name
- Phone
- Email
- Address

### 2. BOOK
- BookID (Primary Key)
- Title
- Author
- Category
- ISBN

### 3. LOAN
- LoanID (Primary Key)
- IssueDate
- DueDate
- ReturnDate
- FineAmount

### 4. EVENT
- EventID (Primary Key)
- EventName
- EventDate
- EventTime
- EventType

### 5. SPEAKER
- SpeakerID (Primary Key)
- SpeakerName
- Topic
- ContactInfo

### 6. ROOM
- RoomID (Primary Key)
- RoomName
- Capacity
- Purpose
- Status

### 7. EVENTREGISTRATION
- RegistrationID (Primary Key)
- RegisterDate
- Status

### 8. EVENTSPEAKER
- EventID (Foreign Key)
- SpeakerID (Foreign Key)

### 9. ROOMBOOKING
- BookingID (Primary Key)
- StartDateTime
- EndDateTime
- BookingType
- Purpose

---

# Relationships and Constraints

### MEMBER — borrows → LOAN
One member can borrow many books through loans.  
Each loan belongs to one member.  
*(1-to-Many)*

### BOOK — involved in → LOAN
One book can appear in many loan records.  
Each loan record is associated with one book.  
*(1-to-Many)*

### MEMBER — registers → EVENTREGISTRATION
One member can register for multiple events.  
Each registration belongs to one member.  
*(1-to-Many)*

### EVENT — contains → EVENTREGISTRATION
One event can have many registrations.  
Each registration belongs to one event.  
*(1-to-Many)*

### EVENT — assigned to → SPEAKER
An event can have multiple speakers.  
A speaker can participate in multiple events.  
*(Many-to-Many)*

### ROOM — booked in → ROOMBOOKING
One room can have many bookings.  
Each booking belongs to one room.  
*(1-to-Many)*

### EVENT — uses → ROOMBOOKING
One event can have multiple room bookings.  
Each room booking belongs to one event.  
*(1-to-Many)*

---

# Assumptions

- Each member must have a valid MemberID.
- A book can be borrowed multiple times but only through separate loan records.
- FineAmount is calculated based on delayed return of books.
- A member can register for multiple events.
- An event may have multiple speakers.
- Each speaker must have at least one topic of specialization.
- Room bookings are required before conducting an event.
- A room cannot be booked for overlapping time periods.
- Attendance and event participation are managed through event registrations.
- EventSpeaker is used to resolve the many-to-many relationship between events and speakers.

---

# Result

Hence, the concepts of ER diagrams are understood and applied by creating an ER diagram for a real-world City Library Event & Book Lending System.
