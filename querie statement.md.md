- \--------------------------------------------------------------------------------

- - SECTION 1: DATABASE CREATION

- \--------------------------------------------------------------------------------

- - Objective: Initialize the dedicated relational database for the hospital management system.

CREATE DATABASE hospital_management_db;

- - Connect to the newly created database (syntax varies by SQL client, e.g., \c in PostgreSQL)

\c hospital_management_db;

- \--------------------------------------------------------------------------------

- - SECTION 2: TABLE CREATION AND CONSTRAINTS

- \--------------------------------------------------------------------------------

- - Objective: Create core entities (patients) and dependent operational tables

- - with primary keys, foreign keys, and cascading delete constraints.

- - 2.1 Master Patient Records Table

CREATE TABLE patients (

patient_id SERIAL PRIMARY KEY,

first_name VARCHAR(50) NOT NULL,

last_name VARCHAR(50) NOT NULL,

date_of_birth DATE NOT NULL,

gender VARCHAR(20),

phone_number VARCHAR(20),

address TEXT,

created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP

);

- - 2.2 Clinical Diagnoses and Conditions Table


CREATE TABLE medical_conditions (

condition_id SERIAL PRIMARY KEY,

patient_id INT REFERENCES patients(patient_id) ON DELETE CASCADE,

diagnosis_name VARCHAR(100) NOT NULL,

diagnosis_date DATE NOT NULL,

status VARCHAR(50) DEFAULT 'Active',

notes TEXT

);

-- 2.3 Surgical Operations Schedule Table

CREATE TABLE surgical_operations (

operation_id SERIAL PRIMARY KEY,

patient_id INT REFERENCES patients(patient_id) ON DELETE CASCADE,

surgery_name VARCHAR(100) NOT NULL,

operation_date TIMESTAMP NOT NULL,

surgeon_name VARCHAR(100),

theater_room VARCHAR(50),

post_op_notes TEXT

);

-- 2.4 Financial Accounts and Billing Table

CREATE TABLE billing (

bill_id SERIAL PRIMARY KEY,

patient_id INT REFERENCES patients(patient_id) ON DELETE CASCADE,

total_amount DECIMAL(10, 2) NOT NULL,

paid_amount DECIMAL(10, 2) DEFAULT 0.00,

payment_status VARCHAR(50) DEFAULT 'Pending',

billing_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP

);


\--------------------------------------------------------------------------------

-- SECTION 3: INITIAL DATA INSERTION

\--------------------------------------------------------------------------------

-- Objective: Populate baseline records for testing and system initialization.

-- 3.1 Insert sample patients

INSERT INTO patients (first_name, last_name, date_of_birth, gender, phone_number, address)

VALUES

('John', 'Smith', '1985-04-12', 'Male', '555-0142', '742 Evergreen Terrace'),

('Emily', 'Johnson', '1990-11-23', 'Female', '555-0188', '104 Westend Avenue');

-- 3.2 Insert initial clinical conditions

INSERT INTO medical_conditions (patient_id, diagnosis_name, diagnosis_date, status, notes)

VALUES

(1, 'Type 2 Diabetes Mellitus', '2026-01-15', 'Chronic', 'Managed via diet and metformin.'),

(2, 'Acute Appendicitis', '2026-10-20', 'Active', 'Scheduled for immediate surgical intervention.');

-- 3.3 Insert scheduled surgical operations

INSERT INTO surgical_operations (patient_id, surgery_name, operation_date, surgeon_name, theater_room, post_op_notes)

VALUES

(2, 'Open Appendectomy', '2026-10-21 09:30:00', 'Dr. Robert Chase', 'OR-3', 'Awaiting clearance.');

-- 3.4 Insert initial billing statements

INSERT INTO billing (patient_id, total_amount, paid_amount, payment_status)

VALUES

(1, 450.00, 450.00, 'Paid'),


(2, 3500.00, 1000.00, 'Partially Paid');

- \--------------------------------------------------------------------------------

- - SECTION 4: SELECT TASKS

- \--------------------------------------------------------------------------------

- - Objective: Query individual table data to verify patient demographics and active clinical statuses.

- - 4.1 Retrieve all registered patient records

SELECT patient_id, first_name, last_name, phone_number

FROM patients;

- - 4.2 Retrieve all active or chronic medical conditions requiring attention

SELECT diagnosis_name, diagnosis_date, status

FROM medical_conditions

WHERE status IN ('Active', 'Chronic');

\--------------------------------------------------------------------------------

-- SECTION 5: JOIN TASKS

\--------------------------------------------------------------------------------

-- Objective: Combine related tables to generate comprehensive cross-functional reports.

-- 5.1 Comprehensive Patient Billing Summary Report

SELECT

p.first_name,

p.last_name,

b.total_amount,

b.paid_amount,


b.payment_status

FROM patients p

JOIN billing b ON p.patient_id = b.patient_id;

-- 5.2 Patient Surgical Schedule and Surgeon Assignment Report

SELECT

p.first_name || ' ' || p.last_name AS patient_name,

s.surgery_name,

s.operation_date,

s.surgeon_name,

s.theater_room

FROM patients p

JOIN surgical_operations s ON p.patient_id = s.patient_id;

- \--------------------------------------------------------------------------------

-- SECTION 6: UPDATE TASKS AND VERIFICATION

- \--------------------------------------------------------------------------------

-- Objective: Update financial ledger records upon final settlement and verify the modification.

-- 6.1 Execute billing update for patient balance settlement

UPDATE billing

SET paid_amount = total_amount, payment_status = 'Paid'

WHERE bill_id = 2;

-- 6.2 Verify the update was successfully applied

SELECT bill_id, patient_id, total_amount, paid_amount, payment_status

FROM billing

WHERE bill_id = 2;


\--------------------------------------------------------------------------------

-- SECTION 7: ALTER TABLE TASKS AND VERIFICATION

\--------------------------------------------------------------------------------

-- Objective: Modify the database schema to capture emergency contact data and verify structure.

-- 7.1 Add emergency contact column to the patients table

ALTER TABLE patients

ADD COLUMN emergency_contact VARCHAR(20);

-- 7.2 Verify structural modification via schema metadata query

SELECT column_name, data_type, character_maximum_length

FROM information_schema.columns

WHERE table_name = 'patients' AND column_name = 'emergency_contact';

\--------------------------------------------------------------------------------

-- SECTION 8: ADDITIONAL INSERT TASKS AND VERIFICATION

\--------------------------------------------------------------------------------

-- Objective: Insert a new patient profile utilizing the updated schema and verify persistence.

-- 8.1 Insert new patient record including the emergency contact field

INSERT INTO patients (first_name, last_name, date_of_birth, gender, phone_number, address, emergency_contact)

VALUES ('Michael', 'Brown', '1978-07-05', 'Male', '555-0199', '88 Grand Boulevard', '555-0100');

-- 8.2 Verify the new entry is correctly stored in the table

SELECT patient_id, first_name, last_name, emergency_contact, created_at


FROM patients

WHERE first_name = 'Michael' AND last_name = 'Brown';
