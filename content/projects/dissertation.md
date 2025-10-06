---
title: "Hospital Appointment & Health Diary App"
---

## Dissertation Project – Hospital Appointment & Health Diary App  

## **June - September 2025**

**Tech stack:** Flutter, Dart, MySQL, OCR (Google ML Kit), HTTP, Git, CLI  

---

## Overview  

Built as my MSc dissertation project, this mobile app supports chronically and seriously ill patients by centralising their healthcare management. It combines appointment scheduling, medication tracking, and daily health logging into one streamlined platform. The system
also supports a user to build a care team on the app to help with the faciliatation of their care and treatment plans. User permissions
were an essential feature to ensure patient health data is secure and private.

---

## Problem → Solution → Impact  

- **Problem:** Patients with complex healthcare needs often struggle with fragmented records and missed routines.  
- **Solution:** An iOS Flutter app with a built-in OCR for scanning appointment letters, medication trackers, and a symptom tracker with sliders (1–10).  
- **Impact:** Reduces stress, improves adherence to treatment plans, and makes communication with care-team easier.  

---

## Screenshots  

| App Home Screen | Text Scan Screen |
|-----------------|------------------|
| {{< figure src="/assets/homePage.PNG" alt="App Home Screen" width="300px">}} | {{< figure src="/assets/textsScan.PNG" alt="Text Scan Screen" width="300px">}} |

---

[Full Code](https://github.com/CondimentKiosk/healthApp)

---

## Code Snippet  

```dart
 Map<String, String> extractAppointmentDetails(String text) {
    final lines = text.split('\n');
    final Map<String, String> info = {};
    final dateRegex = RegExp(
      r'\b(?:Monday|Tuesday|Wednesday|Thursday|Friday|Saturday|Sunday)\b.*\b\d{4}\b',
      caseSensitive: false,
    );
    final timeRegex = RegExp(
      r'\b\d{1,2}:\d{2}\s*(am|pm)\b',
      caseSensitive: false,
    );
    final consultantRegex = RegExp(r'PROJ\s+[A-Z\s]+', caseSensitive: false);
    final hospitalRegex = RegExp(
      r'([A-Z,\s]{6,}HOSPITAL)',
      caseSensitive: false,
    );

    for (var line in lines) {
      final dateMatch = dateRegex.firstMatch(line);
      if (dateMatch != null) {
        info['date'] = dateMatch.group(0)!;
      }

      final timeMatch = timeRegex.firstMatch(line);
      if (timeMatch != null) {
        info['time'] = timeMatch.group(0)!;
      }

      final consultantMatch = consultantRegex.firstMatch(line);
      if (consultantMatch != null) {
        info['consultant'] = consultantMatch.group(0)!;
      }

      final hospitalMatch = hospitalRegex.firstMatch(line);
      if (hospitalMatch != null) {
        info['hospital'] = hospitalMatch.group(0)!;
      }
    }

    return info;
  }

  Widget _appointmentInfoView() {
    if (extracted.isEmpty) return const SizedBox.shrink();

    return Padding(
      padding: const EdgeInsets.all(16.0),
      child: Column(
        crossAxisAlignment: CrossAxisAlignment.start,
        children: [
          Text(
            "Attempting to locate Date, Time, Doctor and Hospital...",
          ),
          Text(
            'Appointment Info',
            style: Theme.of(
              context,
            ).textTheme.titleLarge?.copyWith(fontWeight: FontWeight.bold),
          ),
          const SizedBox(height: 8),
          if (extracted['date'] != null)
            Text(
              '📅 Date: ${extracted['date']}',
              style: Theme.of(context).textTheme.headlineSmall,
            ),
          if (extracted['time'] != null)
            Text(
              '⏰ Time: ${extracted['time']}',
              style: Theme.of(context).textTheme.headlineSmall,
            ),
          if (extracted['consultant'] != null)
            Text(
              '🧑‍⚕️ Consultant: ${extracted['consultant']}',
              style: Theme.of(context).textTheme.headlineSmall,
            ),
          if (extracted['hospital'] != null)
            Text(
              '🏥 Hospital: ${extracted['hospital']}',
              style: Theme.of(context).textTheme.headlineSmall,
            ),
            Text("Extracted Info is not always accurate. Always double check your appointment information."
            "\nYou can change any mistakes from the 'Save Appointment'",
            style: Theme.of(context).textTheme.titleMedium,)
        ],
      ),
    );
  }
```
