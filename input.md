---
title: Input
---

<form id="attendanceForm">
<html lang="en">
<head>
  <meta charset="utf-8" />
  <title>Attendance Input</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <style>
    body { font-family: system-ui, sans-serif; max-width: 720px; margin: 24px auto; padding: 0 16px; }
    form { display: grid; gap: 12px; }
    label { font-weight: 600; }
    input, select, button { padding: 8px; font-size: 16px; }
    .row { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }
    .success { color: #0a7; margin-top: 8px; }
    .error { color: #c00; margin-top: 8px; }
  </style>
</head>
<body>
  <h1>Attendance Input</h1>
  <form id="attendanceForm">
    <div class="row">
      <div>
        <label>Date</label>
        <input type="date" name="date" required />
      </div>
      <div>
        <label>Shift</label>
        <select name="shift" required>
          <option value="Morning">Morning</option>
          <option value="Night">Night</option>
        </select>
      </div>
    </div>

    <div class="row">
      <div>
        <label>Employee Name</label>
        <input type="text" name="employeeName" required />
      </div>
      <div>
        <label>Position</label>
        <input type="text" name="position" required />
      </div>
    </div>

    <div class="row">
      <div>
        <label>Attendance</label>
        <select name="attendance" required>
          <option value="Present">Present</option>
          <option value="Absent">Absent</option>
          <option value="On-Leave">On-Leave</option>
        </select>
      </div>
      <div>
        <label>Location</label>
        <select name="location" required>
          <option>D-1</option><option>D-2</option><option>D-3</option><option>D-4</option>
          <option>D-5</option><option>D-6</option><option>D-7</option><option>D-8</option>
        </select>
      </div>
    </div>

    <button type="submit">Submit</button>
    <div id="msg"></div>
  </form>

  <script>
    const API_POST = "https://your-netlify-site.netlify.app/api/airtable/post";

    document.getElementById("attendanceForm").addEventListener("submit", async (e) => {
      e.preventDefault();
      const msg = document.getElementById("msg");
      msg.textContent = "";

      const fd = new FormData(e.target);
      const payload = {
        Date: fd.get("date"),
        Shift: fd.get("shift"),
        EmployeeName: fd.get("employeeName"),
        Position: fd.get("position"),
        Attendance: fd.get("attendance"),
        Location: fd.get("location")
      };

      try {
        const res = await fetch(API_POST, {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify(payload)
        });
        if (!res.ok) throw new Error("Submit failed");
        msg.textContent = "Record submitted successfully.";
        msg.className = "success";
        e.target.reset();
      } catch (err) {
        msg.textContent = "Error: " + err.message;
        msg.className = "error";
      }
    });
  </script>
</body>
</html>

</form>

<script src="/assets/input.js"></script>
