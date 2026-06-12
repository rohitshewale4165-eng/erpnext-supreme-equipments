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
nts Internship | 13 June 2026*
