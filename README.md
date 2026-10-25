# assignment-III
III

HOSPITAL MANAGEMENT STSTEM 
this is the idea of managing hospital system that track patients from the start of operation to the end of the operation 
So , the system stores patient informatin digitally to avoid pooly lossing of info and miss-manageng and tracking of the patients 
the system stores data of client in form of tables where each and every client data have been stored there in the table which 
structured like this 

there is primary key found column 1 "called name " where the foreign key that act as bridge btn 1st table and other 
called" patient_age " couse it is the reference of patient registry and connect all other tables
here is the visual of our database

https://github.com/rogerdeprogrammer/assignment-III/blob/0b01c94d15d93130b7310dccd9ca5465cd603860/hospital%20ERD.png

and i think it will be better if  i show even the latest result of the tables how iy looks 
in database management system 
https://github.com/rogerdeprogrammer/assignment-III/blob/b4eee0d28fb38077ae5691b5e7dac477af1503be/result%20.png

 so here again is the sql"Queries statement "
 -- Create Patients table
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

-- Create Medical Conditions table
CREATE TABLE medical_conditions (
    condition_id SERIAL PRIMARY KEY,
    patient_id INT REFERENCES patients(patient_id) ON DELETE CASCADE,
    diagnosis_name VARCHAR(100) NOT NULL,
    diagnosis_date DATE NOT NULL,
    status VARCHAR(50) DEFAULT 'Active', -- Active, Resolved, Chronic
    notes TEXT
);

-- Create Surgical Operations table
CREATE TABLE surgical_operations (
    operation_id SERIAL PRIMARY KEY,
    patient_id INT REFERENCES patients(patient_id) ON DELETE CASCADE,
    surgery_name VARCHAR(100) NOT NULL,
    operation_date TIMESTAMP NOT NULL,
    surgeon_name VARCHAR(100),
    theater_room VARCHAR(50),
    post_op_notes TEXT
);

-- Create Billing table
CREATE TABLE billing (
    bill_id SERIAL PRIMARY KEY,
    patient_id INT REFERENCES patients(patient_id) ON DELETE CASCADE,
    total_amount DECIMAL(10, 2) NOT NULL,
    paid_amount DECIMAL(10, 2) DEFAULT 0.00,
    payment_status VARCHAR(50) DEFAULT 'Pending', -- Pending, Paid, Partially Paid
    billing_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

 so as you can see above this step-by-step we used to creat database management system that tracka 
 patient from the begining of the operation to end of it through every step or stage ther are on
 here some info on the table


patients: The core table that stores personal and contact details for each patient (like name, date of birth, and phone number). Every patient gets a unique patient_id.

medical_conditions: Tracks diagnoses, when they were diagnosed, and their current status (e.g., Active or Resolved). It links back to the patient using patient_id.

surgical_operations: Records any surgeries performed on a patient, including the surgeon's name, operation date, and theater room. This also connects to the patient via patient_id.

billing: Manages financial records, tracking total costs, amount paid, and payment status (Pending, Paid, etc.). It links to the respective patient using patient_id.

