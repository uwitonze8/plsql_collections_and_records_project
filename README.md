Student: Uwitonze Pacific  
ID: 26983  
DEPARTMENT: Software Engineering  
Course: Database Development with PL/SQL (INSY 8311)  
Instructor: Eric Maniraguha  


# PL/SQL PROJECT REPORT

## PROJECT TITLE:
Restaurant Reservation Management Using PL/SQL Collections, Records, and GOTO Statements

---

## **Introduction**

Restaurant reservation systems help streamline booking procedures, manage customer seating efficiently, and ensure smooth operations. Oracle PL/SQL combines SQL with procedural programming features, allowing developers to implement real-world business logic more effectively.

This project demonstrates how to develop a simple **Restaurant Reservation Management System** using:

- **PL/SQL Records** for structured data storage  
- **PL/SQL Collections (Nested Tables)** for in-memory data manipulation  
- **GOTO statements** for program flow control  
- **BULK COLLECT** for optimized data retrieval  

The program simulates checking table availability, inserting a new reservation, and displaying available tables using advanced PL/SQL concepts.

---

## **Objectives**

This project aims to:

1. Demonstrate the use of **Records** to group reservation attributes.
2. Use **Collections (Nested Tables)** to process multiple reservation records.
3. Include **GOTO statements** for conditional flow management (e.g., skipping booked tables).
4. Integrate SQL and PL/SQL in building a working system.
5. Apply **BULK COLLECT** for efficient data processing.

---

## **Tools and Environment Used**

| Tool / Software | Description |
|------------------|-------------|
| Oracle Database 21c | Database for PL/SQL execution |
| SQL Developer / SQL*Plus | Environment for running PL/SQL scripts |
| Windows 11 | Operating System |
| DBMS_OUTPUT Package | Used to display program results |

---

# **Problem Definition**

Develop a Restaurant Reservation Management System that can:

- Store reservation data in a relational table  
- Insert a new reservation only if a table is available  
- Fetch all reservations into a collection  
- Use a **GOTO statement** to skip booked tables  
- Display only available tables  

This demonstrates PL/SQL procedural capabilities in a real-world scenario.

---

# **1. Database Design**

## **1.1 Table Creation**

```sql
CREATE TABLE reservations (
  reservation_id NUMBER PRIMARY KEY,
  customer_name  VARCHAR2(100),
  table_number   NUMBER,
  guests         NUMBER,
  reserved_date  DATE,
  is_available   CHAR(1)  -- 'Y' = Available, 'N' = Booked
);



## **2. Main PL/SQL Procedure**

CREATE OR REPLACE PROCEDURE manage_reservations IS

  -- Record for storing reservation details
  TYPE reservation_rec IS RECORD (
      reservation_id reservations.reservation_id%TYPE,
      customer_name  reservations.customer_name%TYPE,
      table_number   reservations.table_number%TYPE,
      guests         reservations.guests%TYPE,
      reserved_date  reservations.reserved_date%TYPE,
      is_available   reservations.is_available%TYPE
  );

  -- Collection of reservation records
  TYPE reservation_table IS TABLE OF reservation_rec;
  reservation_list reservation_table;

  -- Cursor to fetch all reservations
  CURSOR res_cursor IS
      SELECT reservation_id, customer_name, table_number, guests, reserved_date, is_available
      FROM reservations;

  -- Variables for inserting new reservation
  v_new_id NUMBER := 6;
  v_new_name VARCHAR2(100) := 'Uwitonze Pacific';
  v_new_table NUMBER := 2;  -- Trying to reserve a table that may be booked
  v_guests NUMBER := 3;

  v_count NUMBER;

BEGIN

 INSERT NEW RESERVATION SECTION
 

  SELECT COUNT(*) INTO v_count 
  FROM reservations 
  WHERE table_number = v_new_table AND is_available = 'N';

  -- If table is booked → skip insert
  IF v_count > 0 THEN
    DBMS_OUTPUT.PUT_LINE('Table ' || v_new_table || ' is already booked!');
    GOTO skip_insert;
  END IF;

  -- Insert reservation
  INSERT INTO reservations (reservation_id, customer_name, table_number, guests, reserved_date, is_available)
  VALUES (v_new_id, v_new_name, v_new_table, v_guests, SYSDATE, 'N');
  
  COMMIT;

  DBMS_OUTPUT.PUT_LINE('New reservation added for ' || v_new_name);

  <<skip_insert>>
  NULL;

   DISPLAY AVAILABLE TABLES SECTION
  

  OPEN res_cursor;
  FETCH res_cursor BULK COLLECT INTO reservation_list;
  CLOSE res_cursor;

  DBMS_OUTPUT.PUT_LINE(' ');
  DBMS_OUTPUT.PUT_LINE('===== AVAILABLE TABLES =====');

  FOR i IN 1 .. reservation_list.COUNT LOOP

    -- Skip booked tables
    IF reservation_list(i).is_available = 'N' THEN
      GOTO skip_table;
    END IF;

    DBMS_OUTPUT.PUT_LINE('Table: ' || reservation_list(i).table_number ||
                         ' | Customer: ' || reservation_list(i).customer_name ||
                         ' | Guests: ' || reservation_list(i).guests);

    <<skip_table>>
    NULL;

  END LOOP;

  DBMS_OUTPUT.PUT_LINE('===== END OF AVAILABLE TABLES =====');

EXCEPTION
  WHEN OTHERS THEN
    DBMS_OUTPUT.PUT_LINE('Error: ' || SQLERRM);
END;
/


## **3. Execution Command**

SET SERVEROUTPUT ON;
EXEC manage_reservations


##**Conclusion**

This project provides a complete demonstration of core PL/SQL procedural programming concepts. Using collections, records, and GOTO statements, we built a simplified but realistic Restaurant Reservation Management System. The design and logic demonstrate how PL/SQL can improve performance and add flexibility when handling real-world data processing problems.
