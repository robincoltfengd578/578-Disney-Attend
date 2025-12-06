---
title: Summary
---

<div id="summaryView">

<html lang="en">
<head>
  <meta charset="utf-8" />
  <title>Attendance Summary</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <style>
    body { font-family: system-ui, sans-serif; max-width: 960px; margin: 24px auto; padding: 0 16px; }
    .controls { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 12px; margin-bottom: 16px; }
    .card { border: 1px solid #ddd; border-radius: 8px; padding: 12px; margin-bottom: 16px; }
    table { width: 100%; border-collapse: collapse; }
    th, td { padding: 8px; border-bottom: 1px solid #eee; text-align: left; }
    .badge { display:inline-block; padding: 2px 8px; border-radius: 12px; font-size: 12px; }
    .present { background:#d7f7e1; color:#146d1a; }
    .absent { background:#fde0e0; color:#8a1010; }
    .leave { background:#fff5d6; color:#8a6a10; }
  </style>
</head>
<body>
  <h1>Attendance Summary</h1>

  <div class="controls">
    <div>
      <label>Date</label>
      <input type="date" id="dateInput" />
    </div>
    <div>
      <label>Location</label>
      <select id="locationInput">
        <option value="">All</option>
        <option>D-1</option><option>D-2</option><option>D-3</option><option>D-4</option>
        <option>D-5</option><option>D-6</option><option>D-7</option><option>D-8</option>
      </select>
    </div>
    <div style="display:flex;align-items:end;">
      <button id="loadBtn">Load</button>
    </div>
  </div>

  <div id="summaryCard" class="card"></div>
  <div id="locationCard" class="card"></div>
  <div id="tableWrap" class="card">
    <table id="recordsTable">
      <thead>
        <tr>
          <th>Employee Name</th>
          <th>Shift</th>
          <th>Position</th>
          <th>Attendance</th>
          <th>Location</th>
        </tr>
      </thead>
      <tbody></tbody>
    </table>
  </div>

  <script>
    const API_GET = "https://your-netlify-site.netlify.app/api/airtable/get";

    function badge(att) {
      if (att === "Present") return `<span class="badge present">Present</span>`;
      if (att === "Absent") return `<span class="badge absent">Absent</span>`;
      return `<span class="badge leave">On-Leave</span>`;
    }

    async function load() {
      const date = document.getElementById("dateInput").value;
      const location = document.getElementById("locationInput").value;

      const params = new URLSearchParams();
      if (date) params.set("date", date);
      if (location) params.set("location", location);

      const res = await fetch(`${API_GET}?${params.toString()}`);
      const data = await res.json();

      const rows = (data.records || []).map(r => r.fields);

      // Summary counts
      const total = rows.length;
      const present = rows.filter(r => r.Attendance === "Present").length;
      const absent = rows.filter(r => r.Attendance === "Absent").length;
      const leave = rows.filter(r => r.Attendance === "On-Leave").length;
      const capacityPct = total ? Math.round((present / total) * 100) : 0;

      document.getElementById("summaryCard").innerHTML = `
        <div><strong>Date:</strong> ${date || "All"}</div>
        <div><strong>Location:</strong> ${location || "All"}</div>
        <div><strong>Total Scheduled:</strong> ${total}</div>
        <div><strong>Present:</strong> ${present} | <strong>Absent:</strong> ${absent} | <strong>On-Leave:</strong> ${leave}</div>
        <div><strong>Capacity:</strong> ${capacityPct}%</div>
      `;

      // Location-specific table (like your D-5 view)
      const locRows = location ? rows : rows; // already filtered by API if location set
      const locTableHtml = locRows.map(r => `
        <tr>
          <td>${r["Employee Name"] || ""}</td>
          <td>${r.Shift || ""}</td>
          <td>${r.Position || ""}</td>
          <td>${badge(r.Attendance)}</td>
          <td>${r.Location || ""}</td>
        </tr>
      `).join("");

      document.querySelector("#recordsTable tbody").innerHTML = locTableHtml;
      document.getElementById("locationCard").innerHTML = location
        ? `<strong>Selected Location:</strong> ${location} | <strong>Selected Date:</strong> ${date || "All"}`
        : `<strong>All Locations</strong>`;
    }

    document.getElementById("loadBtn").addEventListener("click", load);

    // Defaults: load today's date
    const today = new Date().toISOString().split("T")[0];
    document.getElementById("dateInput").value = today;
    load();
  </script>
</body>
</html>
</div>

<script src="/assets/summary.js"></script>
