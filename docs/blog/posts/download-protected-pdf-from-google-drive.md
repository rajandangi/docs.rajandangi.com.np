---
icon: material/arrow-right
slug: download-protected-pdf-from-google-drive
date: 2025-03-07
authors:
  - rajandangi
categories:
  - Tutorial
  - PDF
  - Google Drive
---
# Download protected pdf from google drive :material-google-drive:

## A Step-by-Step Guide to Downloading Protected PDFs from Google Drive in High Quality

- [x] Open the document in Google Drive
- [x] Scroll to the bottom of the document, so all the pages are present
- [x] Open Developer Tools on a separate window and choose the Console tab
- [x] Paste the code
- [x] Have fun!

```javascript
let trustedURL;
if (window.trustedTypes && trustedTypes.createPolicy) {
    const policy = trustedTypes.createPolicy('myPolicy', {
        createScriptURL: (input) => {
            return input;
        }
    });
    trustedURL = policy.createScriptURL('https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js');
} else {
    trustedURL = 'https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js';
}

// Load the jsPDF library using the trusted URL
let jspdf = document.createElement("script");
jspdf.onload = function () {
    // Create a PDF with higher quality images
    const { jsPDF } = window.jspdf;
    const doc = new jsPDF({
        orientation: 'portrait',
        unit: 'mm',
        format: 'a4'
    });

    let elements = document.getElementsByTagName("img");

    // Function to process each image
    const processImage = (index) => {
        if (index >= elements.length) {
            // All images processed, save the PDF
            doc.save("high_quality_download.pdf");
            return;
        }

        let img = elements[index];
        if (!/^blob:/.test(img.src)) {
            // Skip non-blob images
            processImage(index + 1);
            return;
        }

        // Create a high-resolution canvas
        let canvasElement = document.createElement('canvas');
        // Use double the display size for higher resolution
        canvasElement.width = img.naturalWidth || img.width * 2;
        canvasElement.height = img.naturalHeight || img.height * 2;

        let ctx = canvasElement.getContext("2d");
        ctx.imageSmoothingEnabled = true;
        ctx.imageSmoothingQuality = "high";

        // Draw the image at full resolution
        ctx.drawImage(img, 0, 0, canvasElement.width, canvasElement.height);

        // Get high-quality image data
        let imgData = canvasElement.toDataURL("image/jpeg", 1.0);

        // Calculate dimensions to fit on A4
        const pageWidth = doc.internal.pageSize.getWidth();
        const pageHeight = doc.internal.pageSize.getHeight();

        const imgRatio = canvasElement.height / canvasElement.width;
        let imgWidth = pageWidth - 20; // margins
        let imgHeight = imgWidth * imgRatio;

        // If image is taller than page, scale it down
        if (imgHeight > pageHeight - 20) {
            imgHeight = pageHeight - 20;
            imgWidth = imgHeight / imgRatio;
        }

        // Add a new page for each image except the first
        if (index > 0) {
            doc.addPage();
        }

        // Center the image on the page
        const x = (pageWidth - imgWidth) / 2;
        const y = (pageHeight - imgHeight) / 2;

        doc.addImage(imgData, 'JPEG', x, y, imgWidth, imgHeight);

        // Move to the next image
        processImage(index + 1);
    };

    // Start processing images
    processImage(0);
};

jspdf.src = trustedURL;
document.body.appendChild(jspdf);
```