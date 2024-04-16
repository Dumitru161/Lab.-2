import pickle

class Faculty:
    def init(self, name, field):
        self.name = name
        self.field = field
        self.students = []

    def assign_student(self, student):
        self.students.append(student)

    def graduate_student(self, student):
        if student in self.students:
            self.students.remove(student)

    def enrolled_students(self):
        return [student for student in self.students if not student.graduate_status]

    def graduates(self):
        return [student for student in self.students if student.graduate_status]


class Student:
    def init(self, name, unique_identifier, email):
        self.name = name
        self.unique_identifier = unique_identifier
        self.email = email
        self.graduate_status = False

    def graduate(self):
        self.graduate_status = True


class University:
    def init(self):
        self.faculties = []

    def create_faculty(self, name, field):
        faculty = Faculty(name, field)
        self.faculties.append(faculty)

    def search_faculty(self, unique_identifier):
        for faculty in self.faculties:
            for student in faculty.students:
                if student.unique_identifier == unique_identifier:
                    return faculty
        return None

    def display_all_faculties(self):
        return [faculty.name for faculty in self.faculties]

    def display_faculties_by_field(self, field):
        return [faculty.name for faculty in self.faculties if faculty.field == field]


class SaveManager:
    @staticmethod
    def save_state(university, filename):
        try:
            with open(filename, 'wb') as file:
                pickle.dump(university, file)
                print("Program state saved successfully.")
        except Exception as e:
            print(f"Error occurred while saving program state: {e}")

    @staticmethod
    def load_state(filename):
        try:
            with open(filename, 'rb') as file:
                university = pickle.load(file)
                print("Program state loaded successfully.")
                return university
        except FileNotFoundError:
            print("No saved state found. Starting with empty state.")
            return University()
        except Exception as e:
            print(f"Error occurred while loading program state: {e}")
            return University()


filename = "university_state.pkl"

university = SaveManager.load_state(filename)

while True:
    print("\nMenu:")
    print("1. Faculty Operations")
    print("2. General Operations")
    print("3. Exit")

    choice = input("Enter your choice: ")

    if choice == "1":
        print("\nFaculty Operations:")
        print("1. Create and assign a student to a faculty")
        print("2. Graduate a student from a faculty")
        print("3. Display current enrolled students")
        print("4. Display graduates")
        print("5. Check if a student belongs to a faculty")

        faculty_choice = input("Enter your choice: ")

        if faculty_choice == "1":
            faculty_name = input("Enter faculty name: ")
            faculty_field = input("Enter faculty field: ")
            student_name = input("Enter student name: ")
            student_id = input("Enter student unique identifier: ")
            student_email = input("Enter student email: ")

            student = Student(student_name, student_id, student_email)
            university.create_faculty(faculty_name, faculty_field)
            faculty = university.faculties[-1]
            faculty.assign_student(student)
            print("Student assigned to faculty.")

        elif faculty_choice == "2":
            student_id = input("Enter student unique identifier: ")
            faculty_name = input("Enter faculty name: ")

            faculty = university.search_faculty(student_id)
