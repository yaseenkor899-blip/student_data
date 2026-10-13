import sqlite3
import csv
import os

DB_FILE = "student_data.db"

def connect_db():
    conn = sqlite3.connect(DB_FILE)
    conn.execute("""
        CREATE TABLE IF NOT EXISTS students (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            name TEXT NOT NULL,
            roll_no TEXT UNIQUE NOT NULL,
            department TEXT NOT NULL,
            section TEXT,
            semester TEXT,
            phone TEXT,
            address TEXT
        )
    """)
    conn.commit()
    return conn

def add_student():
    print("\n--- ADD STUDENT ---")
    name = input("Full Name: ").strip()
    roll_no = input("Roll Number: ").strip()
    department = input("Department: ").strip()
    section = input("Section: ").strip()
    semester = input("Semester: ").strip()
    phone = input("Phone: ").strip()
    address = input("Address: ").strip()

    if not name or not roll_no or not department:
        print("Name, Roll Number, and Department are required.")
        return

    try:
        with connect_db() as conn:
            conn.execute("""
                INSERT INTO students
                (name, roll_no, department, section, semester, phone, address)
                VALUES (?, ?, ?, ?, ?, ?, ?)
            """, (name, roll_no, department, section, semester, phone, address))
        print("Student added successfully.")
    except sqlite3.IntegrityError:
        print("This roll number already exists.")

def view_students():
    with connect_db() as conn:
        students = conn.execute("""
            SELECT * FROM students ORDER BY id
        """).fetchall()

    if not students:
        print("\nNo student records found.")
        return

    print("\n--- ALL STUDENTS ---")
    for s in students:
        print("-" * 45)
        print(f"ID: {s[0]}")
        print(f"Name: {s[1]}")
        print(f"Roll Number: {s[2]}")
        print(f"Department: {s[3]}")
        print(f"Section: {s[4]}")
        print(f"Semester: {s[5]}")
        print(f"Phone: {s[6]}")
        print(f"Address: {s[7]}")
    print("-" * 45)
    print(f"Total students: {len(students)}")

def search_student():
    keyword = input("Enter student ID, name, or roll number: ").strip()

    with connect_db() as conn:
        students = conn.execute("""
            SELECT * FROM students
            WHERE CAST(id AS TEXT) = ?
               OR roll_no LIKE ?
               OR name LIKE ?
            ORDER BY id
        """, (keyword, f"%{keyword}%", f"%{keyword}%")).fetchall()

    if not students:
        print("No matching student found.")
        return

    for s in students:
        print("-" * 35)
        print(f"ID: {s[0]} | Name: {s[1]}")
        print(f"Roll Number: {s[2]}")
        print(f"Department: {s[3]} | Section: {s[4]}")
        print(f"Semester: {s[5]}")
        print(f"Phone: {s[6]}")
        print(f"Address: {s[7]}")

def update_student():
    student_id = input("Enter student ID to update: ").strip()

    if not student_id.isdigit():
        print("Please enter a valid numeric ID.")
        return

    with connect_db() as conn:
        student = conn.execute(
            "SELECT * FROM students WHERE id = ?", (int(student_id),)
        ).fetchone()

    if not student:
        print("Student not found.")
        return

    fields = [
        ("Name", student[1]),
        ("Roll Number", student[2]),
        ("Department", student[3]),
        ("Section", student[4] or ""),
        ("Semester", student[5] or ""),
        ("Phone", student[6] or ""),
        ("Address", student[7] or "")
    ]

    updated = []
    for label, old_value in fields:
        value = input(f"{label} [{old_value}]: ").strip()
        updated.append(value if value else old_value)

    if not updated[0] or not updated[1] or not updated[2]:
        print("Name, Roll Number, and Department cannot be empty.")
        return

    try:
        with connect_db() as conn:
            conn.execute("""
                UPDATE students
                SET name = ?, roll_no = ?, department = ?,
                    section = ?, semester = ?, phone = ?, address = ?
                WHERE id = ?
            """, (*updated, int(student_id)))
        print("Student updated successfully.")
    except sqlite3.IntegrityError:
        print("That roll number belongs to another student.")

def delete_student():
    student_id = input("Enter student ID to delete: ").strip()

    if not student_id.isdigit():
        print("Please enter a valid numeric ID.")
        return

    with connect_db() as conn:
        student = conn.execute(
            "SELECT name FROM students WHERE id = ?", (int(student_id),)
        ).fetchone()

        if not student:
            print("Student not found.")
            return

        confirm = input(
            f"Delete {student[0]} permanently? (yes/no): "
        ).strip().lower()

        if confirm == "yes":
            conn.execute(
                "DELETE FROM students WHERE id = ?", (int(student_id),)
            )
            print("Student deleted successfully.")
        else:
            print("Deletion cancelled.")

def export_csv():
    filename = "students_export.csv"

    with connect_db() as conn:
        cursor = conn.execute("""
            SELECT id, name, roll_no, department, section,
                   semester, phone, address
            FROM students ORDER BY id
        """)
        rows = cursor.fetchall()
        headers = [column[0] for column in cursor.description]

    try:
        with open(filename, "w", newline="", encoding="utf-8-sig") as file:
            writer = csv.writer(file)
            writer.writerow(headers)
            writer.writerows(rows)

        print(f"Exported {len(rows)} records to: {os.path.abspath(filename)}")
    except OSError as error:
        print(f"Export failed: {error}")

def main():
    connect_db().close()

    while True:
        print("\n================================")
        print("   STUDENT DATA MANAGEMENT")
        print("================================")
        print("1. Add Student")
        print("2. View All Students")
        print("3. Search Student")
        print("4. Update Student")
        print("5. Delete Student")
        print("6. Export to CSV")
        print("0. Exit")
        print("================================")

        choice = input("Choose an option: ").strip()

        if choice == "1":
            add_student()
        elif choice == "2":
            view_students()
        elif choice == "3":
            search_student()
        elif choice == "4":
            update_student()
        elif choice == "5":
            delete_student()
        elif choice == "6":
            export_csv()
        elif choice == "0":
            print("Program closed.")
            break
        else:
            print("Invalid choice. Enter a number from 0 to 6.")

if __name__ == "__main__":
    main()
