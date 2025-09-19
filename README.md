DROP TABLE IF EXISTS FeePayments;

-- Create FeePayments table
CREATE TABLE FeePayments (
    payment_id INT PRIMARY KEY,
    student_name VARCHAR(100) NOT NULL,
    amount DECIMAL(10,2) CHECK (amount > 0),
    payment_date DATE NOT NULL
);

-- ================================
-- Part A: Insert Multiple Fee Payments in a Transaction (Commit Success)
-- ================================
START TRANSACTION;

INSERT INTO FeePayments (payment_id, student_name, amount, payment_date)
VALUES (1, 'Ashish', 5000.00, '2024-06-01');

INSERT INTO FeePayments (payment_id, student_name, amount, payment_date)
VALUES (2, 'Smaran', 4500.00, '2024-06-02');

INSERT INTO FeePayments (payment_id, student_name, amount, payment_date)
VALUES (3, 'Vaibhav', 5500.00, '2024-06-03');

-- Commit successful inserts
COMMIT;

-- Check inserted records
SELECT * FROM FeePayments;

-- ================================
-- Part B: Demonstrate ROLLBACK for Failed Payment Insertion
-- ================================
START TRANSACTION;

-- First insert is valid
INSERT INTO FeePayments (payment_id, student_name, amount, payment_date)
VALUES (4, 'Kiran', 6000.00, '2024-06-04');

-- Second insert fails (duplicate payment_id = 1 and invalid negative amount)
INSERT INTO FeePayments (payment_id, student_name, amount, payment_date)
VALUES (1, 'Ashish', -2000.00, '2024-06-05');

-- Since error occurs, rollback entire transaction
ROLLBACK;

-- Verify only first 3 records exist
SELECT * FROM FeePayments;

-- ================================
-- Part C: Simulate Partial Failure (NULL student_name)
-- ================================
START TRANSACTION;

-- Valid insert
INSERT INTO FeePayments (payment_id, student_name, amount, payment_date)
VALUES (5, 'Rohit', 4000.00, '2024-06-06');

-- Invalid insert (NULL student_name violates NOT NULL)
INSERT INTO FeePayments (payment_id, student_name, amount, payment_date)
VALUES (6, NULL, 7000.00, '2024-06-07');

-- Rollback due to failure
ROLLBACK;

-- Verify table still has only original 3 rows
SELECT * FROM FeePayments;

-- ================================
-- Part D: Verify ACID Compliance
-- ================================
-- Atomicity: All-or-nothing inserts handled above
-- Consistency: Constraints enforced (PK, NOT NULL, CHECK)
-- Isolation: Use two sessions (not shown in single script)
-- Durability: Committed data persists after COMMIT

-- Final successful insert transaction
START TRANSACTION;

INSERT INTO FeePayments (payment_id, student_name, amount, payment_date)
VALUES (7, 'Meera', 6500.00, '2024-06-08');

COMMIT;

-- Final state of table
SELECT * FROM FeePayments;
