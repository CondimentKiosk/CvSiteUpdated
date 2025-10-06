---
title: "Academic Progression Monitoring System (LMS)"
date: 2025-04-15
---

## Academic Progression Monitoring System (LMS)

## **Febuary - April 2025**

**Tech stack:** JavaScript, MySQL, HTML/CSS, Axios

---

## Overview  

I developed a **role-based Learning Management System (LMS)** designed to monitor student performance and progression in higher education. This project served as a **full-stack** application, incorporating both secure backend logic and dynamic front-end interfaces.  

The system featured **user authentication**, **role-based access control**, and a **dashboard** for visualising student performance metrics.  

---

## Problem → Solution → Impact  

- **Problem:** Universities often lack an efficient, centralised way to track and visualise academic progress across multiple cohorts and subjects.  
- **Solution:** Built a secure, data-driven system that supports multiple user types (Admin, Lecturer, Student), each with tailored access rights. Implemented **SQL joins** and **subqueries** to retrieve performance data, and created a clean, responsive dashboard interface. A batch processor was also built to support CSV uploads to handle large amounts of student data.  
- **Impact:** Improved data visibility and made it easier for lecturers and administrators to monitor progression, while students gained direct access to their own academic insights.  

---

## My Role  

- Designed and implemented the **database schema** (tables, relationships, normalisation).  
- Wrote the **backend logic in JavaScript**, handling user sessions and authentication.  
- Created front-end components for the **performance dashboard** using HTML/CSS.  
- Utilised GitLab for version control and ease of project submission.

---

## Screenshots  

| Login Page | Admin View - All Students |
|-----------------|------------------|
| {{< figure src="/assets/lmsLogin.png" width="1000px">}} | {{< figure src="/assets/lmsAllStudents.png" width="1000px">}} |  

| Lecturer View - Module Students | Lecturer View - Student Progressions |
|-----------------|------------------|
| {{< figure src="/assets/lmsModuleInfo.png" width="1000px">}} | {{< figure src="/assets/lmsStudentProg.png" width="1000px">}} |  

| Student View - Homepage | Student View - Student Progression |
|-----------------|------------------|
| {{< figure src="/assets/lmsStudentHome.png" width="1000px">}} | {{< figure src="/assets/lmsStudentViewProg.png" width="1000px">}} |

---

## Code Snippet  

```html
<!-- Displaying a list of all students -->
 <% if (students.length > 0) { %>
      <table class="uk-table uk-table-hover">
        <thead>
          <tr>
            <th>Student Number</th>
            <th>First Name</th>
            <th>Last Name</th>
            <th>Degree Programme</th>
            <th>Entry Level</th>
            <th>Status of Study</th>
          </tr>
        </thead>
        <tbody>
          <% students.forEach(student => { %>
            <tr>
              <td><%= student.student_number %></td>
              <td><%= student.first_name %></td>
              <td><%= student.last_name %></td>
              <td><%= student.degree_programme %></td>
              <td><%= student.entry_level %></td>
              <td><%= student.status_study %></td>
              <td><a href="/admin/student/<%= student.student_id || 'undefined' %>/edit" class="uk-button uk-button-primary">Edit</a></td>
              <td><form action="/admin/students/<%= student.student_id %>/delete" method="POST" style="display:inline;">
                <input type="hidden" name="student_number" value="<%= student.student_number %>">
                <!-- ai assistance on confirmation before deleting-->
                <button class="uk-button uk-button-danger" onclick="return confirm('Are you sure you want to delete this student?')">
                  Delete
                </button>
              </form></td>
            </tr>
          <% }) %>
        </tbody>
      </table>
    <% } else { %>
      <p class="uk-text-center uk-text-danger">No students found.</p>
    <% } %>
```

```Javascript
//Getting a module grade for student
const getModuleGrade = async (req, res) => {
  const { studentId, moduleId } = req.query;

  try {
    //console.log('Fetching grade for:', { studentId, moduleId });

    //ai assistance for case statement
    const [rows] = await db.query(
      `SELECT s.student_id, s.student_number, s.first_name, s.last_name,
       m.module_title, sg.first_grade, gr.grade_result AS first_result,
       sg.resit_grade, rr.grade_result AS resit_result,
       CASE 
         WHEN rr.grade_result IS NOT NULL THEN rr.grade_result
         ELSE gr.grade_result
       END AS final_result
FROM Student_Grade sg
LEFT JOIN Students s ON sg.student_id = s.student_id
LEFT JOIN Modules m ON sg.module_id = m.module_id
LEFT JOIN Grade_Result gr ON sg.grade_result_id = gr.grade_result_id
LEFT JOIN Grade_Result rr ON sg.resit_result_id = rr.grade_result_id
WHERE sg.student_id = ? AND sg.module_id = ?
LIMIT 1;`,
      [studentId, moduleId]);

    console.log('Query result:', rows);

    res.json({ success: true, details: rows });

  } catch (err) {
    console.error(err);
    res.json({ success: false, error: 'Failed to fetch module grades' });
  }
};
