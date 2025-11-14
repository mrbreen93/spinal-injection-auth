<!DOCTYPE html>

<html lang="en">

<head>

&nbsp; <meta charset="UTF-8">

&nbsp; <meta name="viewport" content="width=device-width, initial-scale=1.0">

&nbsp; <title>Spinal Injection Prior Authorization Analyzer</title>

&nbsp; <style>

&nbsp;   body { font-family: Arial, sans-serif; margin: 30px; background-color: #f4f4f4; }

&nbsp;   h1 { color: #333; }

&nbsp;   label { display: block; margin-top: 15px; font-weight: bold; }

&nbsp;   select, textarea { width: 100%; padding: 8px; margin-top: 5px; }

&nbsp;   button { margin-top: 20px; padding: 10px 20px; background-color: #0072CE; color: white; border: none; cursor: pointer; }

&nbsp;   button:hover { background-color: #005BB5; }

&nbsp;   .result { margin-top: 30px; padding: 20px; background-color: #fff; border-left: 5px solid #0072CE; }

&nbsp;   .warning { border-left-color: #E74C3C; }

&nbsp;   .success { border-left-color: #2ECC71; }

&nbsp; </style>

</head>

<body>

&nbsp; <h1>Spinal Injection Prior Authorization Analyzer</h1>



&nbsp; <label for="insurance">Insurance Carrier</label>

&nbsp; <select id="insurance">

&nbsp;   <option value="">-- Select Insurance --</option>

&nbsp;   <option value="uhc">UnitedHealthcare</option>

&nbsp;   <option value="bcbs">Blue Cross Blue Shield</option>

&nbsp;   <option value="aetna">Aetna</option>

&nbsp;   <option value="cigna">Cigna</option>

&nbsp;   <option value="other">Other Commercial</option>

&nbsp; </select>



&nbsp; <label for="procedure">Procedure Requested</label>

&nbsp; <select id="procedure">

&nbsp;   <option value="">-- Select Procedure --</option>

&nbsp;   <option value="facet">Facet Injection / MBB / RFA</option>

&nbsp;   <option value="epidural">Epidural Steroid Injection</option>

&nbsp; </select>



&nbsp; <label for="note">Paste Patient Office Note:</label>

&nbsp; <textarea id="note" rows="15" placeholder="Paste full office note here..."></textarea>



&nbsp; <button onclick="analyzeNote()">Analyze Authorization</button>



&nbsp; <div id="result" class="result"></div>



&nbsp; <script>

&nbsp;   function analyzeNote() {

&nbsp;     const insurance = document.getElementById('insurance').value;

&nbsp;     const procedure = document.getElementById('procedure').value;

&nbsp;     const noteText = document.getElementById('note').value.toLowerCase();

&nbsp;     const resultDiv = document.getElementById('result');

&nbsp;     resultDiv.className = 'result';

&nbsp;     let missing = \[];



&nbsp;     if (!insurance || !procedure || !noteText) {

&nbsp;       resultDiv.classList.add('warning');

&nbsp;       resultDiv.innerHTML = "<strong>Error:</strong> Please select insurance, procedure, and paste the office note.";

&nbsp;       return;

&nbsp;     }



&nbsp;     // Keyword detection lists

&nbsp;     const axialPainKeywords = \["axial", "central low back", "facet pain", "paraspinal"];

&nbsp;     const facetExamKeywords = \["facet loading", "kemp", "palpation tenderness", "facet provocation"];

&nbsp;     const conservativeKeywords = \["physical therapy", "pt", "medication", "cymbalta", "gabapentin", "nsaids", "ibuprofen"];

&nbsp;     const imagingKeywords = \["facet hypertrophy", "facet fluid", "facet arthropathy", "l3-4", "l4-5", "l5-s1", "herniation", "stenosis"];

&nbsp;     const rfaKeywords = \["radiofrequency", "rfa", "denervation"];

&nbsp;     const epiduralKeywords = \["epidural", "esi", "steroid injection", "transforaminal", "interlaminar"];



&nbsp;     // Detection

&nbsp;     const hasAxialPain = axialPainKeywords.some(word => noteText.includes(word));

&nbsp;     const hasFacetExam = facetExamKeywords.some(word => noteText.includes(word));

&nbsp;     const hasConservative = conservativeKeywords.some(word => noteText.includes(word));

&nbsp;     const hasImaging = imagingKeywords.some(word => noteText.includes(word));

&nbsp;     const hasRFA = rfaKeywords.some(word => noteText.includes(word));

&nbsp;     const hasPriorESI = epiduralKeywords.some(word => noteText.includes(word));



&nbsp;     if (procedure === "facet") {

&nbsp;       if (!hasAxialPain) missing.push("Document axial facet pain");

&nbsp;       if (!hasFacetExam) missing.push("Document facet-loading physical exam");

&nbsp;       if (!hasConservative) missing.push("Document 4–6 week conservative therapy/PT trial");

&nbsp;       if (!hasImaging) missing.push("Correlate imaging with pain location");

&nbsp;       if (!hasRFA) missing.push("Document plan for RFA contingent on diagnostic block");



&nbsp;       if (missing.length === 0) {

&nbsp;         resultDiv.classList.add('success');

&nbsp;         resultDiv.innerHTML = "<strong>Likely Approved</strong> – Documentation meets typical commercial insurance criteria for facet injections / MBB / RFA.";

&nbsp;       } else {

&nbsp;         resultDiv.classList.add('warning');

&nbsp;         resultDiv.innerHTML = "<strong>Likely Denied / Needs More Documentation</strong><br>Missing Items:<ul><li>" + missing.join("</li><li>") + "</li></ul>";

&nbsp;       }

&nbsp;     }



&nbsp;     if (procedure === "epidural") {

&nbsp;       if (!hasConservative) missing.push("Document 4–6 week conservative therapy/PT/medications trial");

&nbsp;       if (!hasImaging) missing.push("Document MRI/CT imaging correlating with radicular or axial pain");

&nbsp;       if (!hasPriorESI) missing.push("Document prior ESIs and response");



&nbsp;       if (missing.length === 0) {

&nbsp;         resultDiv.classList.add('success');

&nbsp;         resultDiv.innerHTML = "<strong>Likely Approved</strong> – Documentation meets typical commercial insurance criteria for epidural steroid injection.";

&nbsp;       } else {

&nbsp;         resultDiv.classList.add('warning');

&nbsp;         resultDiv.innerHTML = "<strong>Likely Denied / Needs More Documentation</strong><br>Missing Items:<ul><li>" + missing.join("</li><li>") + "</li></ul>";

&nbsp;       }

&nbsp;     }

&nbsp;   }

&nbsp; </script>

</body>

</html>



