## Query Sql

1. Tampilkan daftar siswa beserta kelas dan guru yang mengajar kelas tersebut.

```
SELECT
	students.name AS student_name,
	classes.name AS class_name,
	teachers.name AS teacher_name
FROM
    students
INNER JOIN
	classes ON students.class_id = classes.id
INNER JOIN
	teachers ON classes.teacher_id = teachers.id
```

2. Tampilkan daftar kelas yang diajar oleh guru yang sama.

```
SELECT
    teachers.name AS teacher_name,
    GROUP_CONCAT(classes.name SEPARATOR ', ') AS class_list
FROM
    classes
INNER JOIN
    teachers ON classes.teacher_id = teachers.id
GROUP BY
    teachers.name;
```

3. buat query view untuk siswa, kelas, dan guru yang mengajar

```
CREATE VIEW student_class_teacher AS
SELECT
    students.name AS student_name,
    classes.name AS class_name,
    teachers.name AS teacher_name
FROM
    students
INNER JOIN
    classes ON students.class_id = classes.id
INNER JOIN
    teachers ON classes.teacher_id = teachers.id;

==================================================

SELECT * FROM student_class_teacher;
```

4. buat query yang sama tapi menggunakan store_procedure

```
DELIMITER $$

CREATE PROCEDURE GetStudentClassTeacher()
BEGIN
    SELECT
        students.name AS student_name,
        classes.name AS class_name,
        teachers.name AS teacher_name
    FROM
        students
    INNER JOIN
        classes ON students.class_id = classes.id
    INNER JOIN
        teachers ON classes.teacher_id = teachers.id;
END$$

DELIMITER ;

```

5. buat query input, yang akan memberikan warning error jika ada data yang sama pernah masuk

```
DELIMITER $$

CREATE TRIGGER before_student_insert
BEFORE INSERT ON students
FOR EACH ROW
BEGIN
    DECLARE msg VARCHAR(255);
    IF EXISTS (SELECT 1 FROM students WHERE name = NEW.name AND class_id = NEW.class_id) THEN
        SET msg = 'Error: Student with the same name already exists in this class.';
        SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = msg;
    END IF;
END$$

DELIMITER ;

```
