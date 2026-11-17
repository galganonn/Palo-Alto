# Incident Investigation — Project Brief
> מטלת בית Palo Alto Networks / Cortex · מסמך המשכיות לצ'אט חדש

---

## המשימה

עיצוב מסך **Incident Investigation** עבור פלטפורמת SOC של Cortex (XDR/XSIAM).  
לא עיצוב בפיגמה — בניית HTML mockups אינטראקטיביים ישירות בצ'אט.

**Stack:** Vanilla HTML/CSS/JS בלבד. אין frameworks, אין npm.  
**Git remote:** https://github.com/galganonn/Palo-Alto (לא לדחוף בלי אישור מפורש)

---

## הקונטקסט הארגוני

- **חברה:** Palo Alto Networks, חטיבת Cortex
- **מגייס:** הגדיר ויז'ן "agentic-first, 2 years from now"
- **גל:** Senior Product Designer, עובדת על מטלת בית

---

## הפרסונה: SOC Analyst (Tier 1–2)

אנליסט במרכז אבטחה שמתנהל בתנאי לחץ קיצוני:

- מקבל 100–200 התראות ביום, ~70% false positives
- עובר בין 10+ כלים במקביל (SIEM, EDR, Threat Intel, ticketing)
- יש לו 15–30 דקות לחקור incident לפני שצריך להחליט
- **כאב מרכזי:** cognitive overload + alert fatigue. לא חסר מידע — חסרה בהירות.

---

## מבנה ה-Incident

- **Alert** = אירוע אבטחה בודד (חתימה על process חשוד, login כושל וכו')
- **Incident** = קבוצת alerts שה-AI קישר לסיפור התקפה אחד
- כל incident מכיל: metadata (ID, שם, severity, status), 2–100+ alerts, נכסים מושפעים (hosts, users), MITRE ATT&CK mapping, timeline, causality chain

---

## הוויז'ן: Agentic-First Design

**פרדיגמה חדשה:** האי עושה את החקירה. האנליסט הוא Reviewer, לא Investigator.

| לפני | אחרי |
|---|---|
| אנליסט קורא 23 alerts ידנית | AI מסכם: "זה מה שקרה" |
| אנליסט מחפש פאטרנים | AI מציג: "אלה 4 הרגעים הקריטיים" |
| אנליסט מחליט לבד | AI מציע: "אלה 3 הפעולות, אשר/דחה" |

**השראה:** סרטון של Yair Cohen מ-g1 labs על Generative UI — AI מחזיר קומפוננטות UI אינטראקטיביות inline בצ'אט, לא טקסט.

---

## הקונספט: Chat + Generative UI

כשאנליסט לוחץ על incident בטבלה:
1. נפתח מסך חדש עם breadcrumb פעיל
2. AI כבר ביצע חקירה מלאה
3. התוצאה מוצגת כ**כרטיס generated אחד** עם כל התוכן
4. מתחת לכרטיס: **chat box** לשאלות המשך

---

## שכבת האמון (Trust Layer) — קריטי

האנליסטים עדיין **לא סומכים על AI**. לכן כל claim חייב:

- מאיפה לקוח המידע (source tag על כל event)
- כמה האי בטוח ולמה (Confidence % + expandable sources)
- מה האי **לא** יודע (Callout צהוב: "Cannot confirm: data exfiltration")
- כמה הפעולה הפיכה (Reversible / Can't be undone)

---

## מידע שמופיע בכרטיס

### Threat Summary
- נרטיב טקסטואלי (פסקה אחת — "מה קרה")
- 4 stats: מספר alerts / hosts / משך זמן / WildFire hits
- MITRE ATT&CK tactics pills
- Confidence % עם expandable sources (3 confirmed + 1 uncertain)
- Callout: מה האי לא יכול לאשר

### Attack Timeline
- 4 מתוך N אירועים קריטיים (לא כולם)
- כל אירוע: זמן + תיאור + source tag
- אירועים עם ביטחון נמוך מסומנים ⚠

### Response Actions — Approval Request pattern
כל אחת מ-3 הפעולות כוללת:
- **Scope:** בדיוק מה ייפגע (hostname, user, מספר endpoints)
- **What happens:** 3 bullets של תוצאות ספציפיות
- **Reversibility:** Reversible ✓ / Can't be undone ✗ (אדום)
- **Or instead:** פעולה חלופית פחות אגרסיבית
- כפתורי Approve / Reject

---

## Layout (גרסה V2 — הנוכחית)

```
[Sidebar nav — icons]
[Breadcrumb: Incidents › שם האינסידנט · #INC-4471 · Critical · Under investigation]
[Generated Investigation Card — כרטיס אחד גדול]
  ├── Threat Summary (narrative + stats + tactics + confidence)
  ├── Attack Timeline (horizontal, 4 events)
  └── Response Actions (3 Approval Request cards, collapsible)
[Chat box + Follow-up Chips]
```

**צבעוניות:** dark theme.
- Background: `#0b0d13`
- Surface: `#141828`
- AI accent: `#6b5fe3` (purple)
- Critical: `#ef4444`
- Warning: `#f59e0b`
- OK / Approved: `#22c55e`

---

## ספריית הקומפוננטות: g1-labs

**אתר:** https://g1-labs.dev  
122 קומפוננטות Generative UI מאורגנות ב-10 קטגוריות.

### קומפוננטות רלוונטיות — Tier 1

| קומפוננטה | קטגוריה | שימוש |
|---|---|---|
| **Summary Card** | Compose | כרטיס Threat Summary |
| **Timeline** | Visualize | Attack timeline עם states |
| **Callout** | Compose | Warning / "Cannot confirm" (3 variants: Warning/Critical/Note) |
| **Follow-up Chips** | Compose | Suggested questions בחלק chat |
| **Approval Request** | Act | כל response action |
| **Confidence** | Transparency | 94% + evidence for/against expandable |
| **Citations** | Transparency | Source attribution על כל event |
| **Assumptions** | Transparency | מה האי לא יודע + מה הוא הניח |

### קומפוננטות רלוונטיות — Tier 2 (לתרחישים נוספים)

| קומפוננטה | מתי |
|---|---|
| **Activity Trail** | "How did you investigate?" — רשימת tool calls |
| **Data Table** | Full alerts list (on demand) |
| **Task Queue** | אלטרנטיבה ל-Approval Request בתור ממוספר |
| **Execution Progress** | כשהאי עדיין חוקר |
| **KPI Card** | Stats row (23 alerts / 2 hosts / 11m) |

### עקרונות g1 labs (חשוב לעיצוב)
1. **Show UI only when interaction is needed** — לפעמים טקסט עדיף על ווידג'ט
2. **Transparency before action** — irreversible = חייב להיות מסומן מפורש
3. **Streamable** — כרטיסים מרנדרים תוך streaming (skeleton → partial → complete)
4. **Data-bound, not guessed** — כל שדה חייב לבוא ממקור אמיתי

---

## מה שנבנה עד עכשיו

שלוש גרסאות של המסך כ-HTML widgets:

1. **V1** — Chat-based (messages + generated cards interleaved)
2. **V2** — Single generated card + chat box below (הגרסה הנוכחית)
3. **Actions section** — updated לפי Approval Request pattern מ-g1

---

## מה שנשאר לבנות (V3 מלאה)

גרסה מלאה שמשלבת הכל:

- [ ] Layout V2 (sidebar + breadcrumb + single card + chat)
- [ ] Threat Summary עם Confidence component (expandable sources)
- [ ] Callout עם Assumptions ("What this is based on" + "Not known")
- [ ] Timeline עם source tags ו-uncertainty markers
- [ ] Actions כ-Approval Request cards (collapsible, Scope + What happens + Or instead)
- [ ] Follow-up Chips בחלק ה-chat box
- [ ] Streaming skeleton state (optional — אנימציית loading לפני שהכרטיס מופיע)

---

## דוגמת incident לעבוד איתה

```
Incident: #INC-4471 · "Credential theft + lateral movement"
Severity: Critical · Status: Under investigation
Duration: 11 minutes active
Assigned: Sarah M.

Timeline:
14:31 — VPN login from Thailand (john.doe) · Identity logs
14:35 — Credential dump via mimikatz on WORKSTATION-14 · Endpoint agent
14:38 — Lateral movement → SERVER-02 via RDP · Network logs (⚠ low confidence)
14:42 — Malware dropped: lockbit_dropper.exe · WildFire confirmed

Stats: 23 alerts / 2 hosts (WORKSTATION-14, SERVER-02) / 1 WildFire hit
MITRE: Initial Access · Credential Access · Lateral Movement · Execution
Confidence: 94% (3 confirmed signals, 1 uncertain)
Cannot confirm: data exfiltration

Actions:
1. Isolate WORKSTATION-14 · High risk · Reversible
2. Reset credentials john.doe · Medium risk · Reversible
3. Block hash a3f9c2b1... org-wide · High risk · Can't be undone
```
