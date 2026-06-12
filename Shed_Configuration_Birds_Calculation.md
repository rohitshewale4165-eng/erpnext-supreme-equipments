# Shed Configuration & Birds Calculation
**Company:** Supreme Equipments  
**Platform:** ERPNext (innosphere.m.erpnext.com)  
**Date:** 12 June 2026  
**Intern:** Rohit Shewale  

---

## Live Links
- [Shed Configuration](https://innosphere.m.erpnext.com/app/shed-configuration)
- [Birds Calculation](https://innosphere.m.erpnext.com/app/shed-configuration/birds%20Calculation)

---

## Project Overview

Poultry shed ka size input karo → Automatically calculate hoga:
- Kitna cage length hoga
- Kitne female birds fit honge
- Kitne male birds fit honge
- Total birds count

```
Shed Size (Input)
      ↓
Cage Length = Shed Length - 10
      ↓
Female Sections (100% of cage)
Male Sections (10% of cage)
      ↓
Total Birds = Female + Male (Output)
```

---

## DocType 1 — Shed Configuration

**Purpose:** User yahan shed ki details daalta hai

| Field Label | Fieldname | Type | Mandatory |
|---|---|---|---|
| Shed Length (ft) | shed_length | Float | ✅ |
| Shed Width (ft) | shed_width | Float | ✅ |
| Shed Height (ft) | shed_height | Float | ❌ |
| No of Rows | no_of_rows | Int | ✅ |
| No of Tiers | no_of_tiers | Int | ✅ |
| Birds per Box | birds_per_box | Int | ✅ |

---

## DocType 2 — Birds Calculation

**Purpose:** Shed select karo → sab automatically calculate hoga

| Field Label | Fieldname | Type | Read Only |
|---|---|---|---|
| Shed Configuration | shed_configuration | Link | ❌ |
| Cage Length (ft) | cage_length | Float | ✅ |
| Female Sections | female_sections | Float | ✅ |
| Male Sections | male_sections | Float | ✅ |
| Total Female Birds | total_female_birds | Int | ✅ |
| Total Male Birds | total_male_birds | Int | ✅ |
| Total Birds | total_birds | Int | ✅ |

---

## Client Script

**Script Name:** Shed Birds Calculation  
**DocType:** Birds Calculation  
**Trigger:** shed_configuration field change + form refresh  

```javascript
frappe.ui.form.on('Birds Calculation', {
    shed_configuration: function(frm) {
        calculate_birds(frm);
    },
    refresh: function(frm) {
        calculate_birds(frm);
    }
});

function calculate_birds(frm) {
    if (frm.doc.shed_configuration) {
        frappe.db.get_doc('Shed Configuration', frm.doc.shed_configuration)
        .then(function(shed) {

            // Step 1 — Cage Length
            let cage_length = shed.shed_length - 10;

            // Step 2 — Female Sections
            let two_tier_female   = cage_length * 0.36;
            let three_tier_female = cage_length - two_tier_female;
            let female_sections   = two_tier_female + three_tier_female;

            // Step 3 — Male Sections
            let male_sections = cage_length * 0.10;

            // Step 4 — Total Birds
            let total_female_birds = Math.round(
                female_sections * shed.birds_per_box * shed.no_of_rows
            );
            let total_male_birds = Math.round(
                male_sections * shed.birds_per_box * shed.no_of_rows
            );
            let total_birds = total_female_birds + total_male_birds;

            // Step 5 — Form mein set karo
            frm.set_value('cage_length',        cage_length);
            frm.set_value('female_sections',    female_sections);
            frm.set_value('male_sections',      male_sections);
            frm.set_value('total_female_birds', total_female_birds);
            frm.set_value('total_male_birds',   total_male_birds);
            frm.set_value('total_birds',        total_birds);
        });
    }
}
```

---

## Calculation Logic

| Step | Formula | Example |
|---|---|---|
| Cage Length | shed_length - 10 | 270 - 10 = **260 ft** |
| 2-Tier Female | cage_length × 36% | 260 × 0.36 = **93.6** |
| 3-Tier Female | cage_length - 2tier | 260 - 93.6 = **166.4** |
| Female Sections | 2tier + 3tier | 93.6 + 166.4 = **260** |
| Male Sections | cage_length × 10% | 260 × 0.10 = **26** |
| Total Female Birds | female × birds × rows | 260 × 2 × 3 = **1560** |
| Total Male Birds | male × birds × rows | 26 × 2 × 3 = **156** |
| Total Birds | female + male | 1560 + 156 = **1716** |

---

## Test Case

**Input:**
```
Shed Length  : 270 ft
Shed Width   : 35 ft  
No of Rows   : 3
No of Tiers  : 3
Birds per Box: 2
```

**Output:**
```
Cage Length        : 260 ft  ✅
Female Sections    : 260     ✅
Male Sections      : 26      ✅
Total Female Birds : 1560    ✅
Total Male Birds   : 156     ✅
Total Birds        : 1716    ✅
```

---

## Concepts Used

| Concept | Use |
|---|---|
| Link Field | Shed Configuration ko Birds Calculation se connect kiya |
| Client Script | JavaScript se automatic calculation |
| frappe.db.get_doc | Database se shed data fetch kiya |
| frm.set_value | Calculated values form mein set kiye |
| Math.round | Decimal values round off kiye |
| refresh trigger | Form open hone pe auto recalculate |

---

*Rohit Shewale | Supreme Equipments Internship | 13 June 2026*
