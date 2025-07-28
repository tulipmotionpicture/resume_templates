# Resume Template Design Guidelines

## Table of Contents
1. [Overview](#overview)
2. [Technical Requirements](#technical-requirements)
3. [Data Structure Integration](#data-structure-integration)
4. [Template Architecture](#template-architecture)
5. [Styling Guidelines](#styling-guidelines)
6. [Interactive Features](#interactive-features)
7. [Responsive Design](#responsive-design)
8. [Testing & Validation](#testing--validation)
9. [Best Practices](#best-practices)
10. [Common Pitfalls](#common-pitfalls)

---

## Overview

This guide provides comprehensive instructions for designing professional resume templates that integrate seamlessly with our resume builder system. Templates must support dynamic data provisioning, drag-and-drop functionality, and maintain professional appearance across all devices.

### Core Principles
- **Professional Appearance**: Clean, modern designs suitable for corporate environments
- **Data-Driven**: All content dynamically populated from JSON payload
- **Interactive**: Drag-and-drop section reordering within containers
- **Responsive**: Mobile-friendly layouts that maintain readability
- **Accessible**: Proper semantic HTML and text overflow handling

---

## Technical Requirements

### 1. File Structure
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Professional Resume Template [N]</title>
    <script>
        // PostMessage listener comment
    </script>
    <style>
        /* All CSS inline for portability */
    </style>
</head>
<body>
    <!-- Template content -->
    <script>
        /* JavaScript functionality */
    </script>
</body>
</html>
```

### 2. Essential Meta Tags
```html
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

### 3. Self-Contained Design
- **All CSS inline** - No external stylesheets
- **All JavaScript inline** - No external scripts
- **No external dependencies** - No CDN links or external resources
- **No icons or emojis** - Text-based design only

---

## Data Structure Integration

### 1. PostMessage Listener (Required)
```javascript
// Listen for messages from parent window (ResumePreview.tsx)
window.addEventListener("message", function (event) {
    console.log("Message received:", event.data);
    if (event.data && event.data.type === "RESUME_DATA") {
        console.log("Processing resume data:", event.data.payload);
        renderResumeData(event.data.payload);
    }
});
```

### 2. Data Payload Structure
Your template must handle this exact JSON structure:

```javascript
{
    "templateId": "8",
    "personalInfo": {
        "id": "2",                          // ❌ Don't render IDs
        "fullName": "Somesh SD",            // ✅ Use this (not 'name')
        "email": "user@example.com",
        "phone": "+1-555-123-4567",
        "address": "Pune, MH",              // ✅ Use this (not 'location')
        "professionalTitle": "architect",   // ✅ Use this (not 'title')
        "professionalSummary": "...",       // ✅ Use this (not 'summary')
        "website": "https://example.com",
        "linkedin": "https://linkedin.com/in/user"
    },
    "workHistory": [                        // ✅ Use this (not 'workExperience')
        {
            "id": "4",                      // ❌ Don't render IDs
            "company": "Company Name",
            "position": "Job Title",
            "location": "City, State",
            "startDate": "2025-07-09",
            "endDate": "",                  // Empty if current
            "current": true,                // ✅ Use for "Present"
            "description": "Job description",
            "isCurrent": true               // ✅ Alternative current flag
        }
    ],
    "education": [
        {
            "id": "4",                      // ❌ Don't render IDs
            "degree": "Bachelor of Science",
            "fieldOfStudy": "Computer Science", // ✅ Combine with degree
            "university": "University Name",     // ✅ Use this (not 'institution')
            "startYear": "2015",                // ✅ Use this (not 'startDate')
            "endYear": "2019",                  // ✅ Use this (not 'endDate')
            "gpa": "3.7/4.0",                   // ✅ Display if available
            "location": "City, State"
        }
    ],
    "skills": [
        {
            "id": "49",                     // ❌ Don't render IDs
            "name": "JavaScript",
            "proficiencyLevel": 10,         // ✅ Use this (not 'proficiency')
            "status": "ACTIVE"              // ❌ Don't render status
        }
    ],
    "languages": [                          // ✅ Optional section
        {
            "id": "1",                      // ❌ Don't render IDs
            "name": "English",
            "proficiency": "Native"
        }
    ],
    "accomplishments": [                    // ✅ Optional section (array of strings)
        "Employee of the Year 2022"
    ],
    "interests": [                          // ✅ Optional section (array of strings)
        "Hiking", "Photography"
    ],
    "certifications": [                     // ✅ Optional section
        {
            "id": "1",                      // ❌ Don't render IDs
            "name": "AWS Certified Developer",
            "issuer": "Amazon",
            "date": "2021-05"
        }
    ]
}
```

### 3. Field Mapping Requirements

#### Personal Information
```javascript
// ✅ CORRECT - Use these field names
data.personalInfo.fullName          // Not 'name'
data.personalInfo.professionalTitle // Not 'title'  
data.personalInfo.professionalSummary // Not 'summary'
data.personalInfo.address           // Not 'location'

// ✅ STANDARD - These are correct
data.personalInfo.email
data.personalInfo.phone
data.personalInfo.linkedin
data.personalInfo.website
```

#### Skills
```javascript
// ✅ CORRECT - Primary field
skill.proficiencyLevel              // Not 'proficiency' or 'level'

// ✅ FALLBACK - For compatibility
skill.proficiencyLevel || skill.proficiency || skill.level || 5
```

#### Work Experience
```javascript
// ✅ CORRECT - Use workHistory array
data.workHistory                    // Not 'workExperience'

// ✅ CURRENT JOB - Handle properly
const endDate = exp.current || exp.isCurrent ? 'Present' : (exp.endDate || '');
```

#### Education
```javascript
// ✅ CORRECT - Combine degree fields
const degreeText = edu.degree && edu.fieldOfStudy ? 
    `${edu.degree} in ${edu.fieldOfStudy}` : 
    (edu.degree || edu.title || '');

// ✅ CORRECT - Use university field
edu.university || edu.institution || edu.school || ''

// ✅ CORRECT - Use year fields
const dateRange = edu.startYear && edu.endYear ? 
    `${edu.startYear} - ${edu.endYear}` : 
    `${edu.startDate || ''} - ${edu.endDate || ''}`;

// ✅ CORRECT - Display GPA if available
edu.gpa ? `GPA: ${edu.gpa}` : (edu.description || '')
```

---

## Template Architecture

### 1. Container Structure
```html
<div class="container">
    <!-- Template content with proper sizing -->
</div>
```

**Container Requirements:**
```css
.container {
    max-width: 210mm;          /* A4 paper width */
    margin: 20px auto;         /* Center with margins */
    background: white;
    box-shadow: 0 0 15px rgba(0,0,0,0.1);
    min-height: 297mm;         /* A4 paper height */
}
```

### 2. Section Structure
Every draggable section must follow this pattern:

```html
<div class="section draggable" data-section="sectionName">
    <h2 class="section-title">Section Title</h2>
    <!-- Section content -->
</div>
```

**Required Attributes:**
- `class="section draggable"` - Enables drag functionality
- `data-section="sectionName"` - Identifies section type for data binding

**Standard Section Names:**
- `data-section="summary"` - Professional summary
- `data-section="experience"` - Work experience
- `data-section="education"` - Education history
- `data-section="skills"` - Skills and proficiencies
- `data-section="contact"` - Contact information (if separate section)

### 3. Layout Patterns

#### Two-Column Layout
```css
.container {
    display: grid;
    grid-template-columns: 1fr 2fr; /* Sidebar + Main */
    /* OR */
    grid-template-columns: 1fr 1fr; /* Equal columns */
}
```

#### Header + Content Layout
```css
.container {
    display: grid;
    grid-template-rows: auto 1fr;
}
```

#### Single Column Layout
```css
.container {
    padding: 40px;
    /* Single column with sections stacked vertically */
}
```

---

## Styling Guidelines

### 1. Professional Color Palette
```css
/* Primary Colors - Choose ONE set */

/* Option 1: Navy Professional */
--primary: #2c3e50;
--secondary: #3498db;
--text: #333;
--light-bg: #f8f9fa;

/* Option 2: Corporate Blue */
--primary: #1a365d;
--secondary: #4a5568;
--text: #2d3748;
--light-bg: #f7fafc;

/* Option 3: Charcoal Modern */
--primary: #2c2c2c;
--secondary: #666;
--text: #333;
--light-bg: #f5f5f5;
```

### 2. Typography Standards
```css
/* Font Families - Choose ONE */
font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; /* Modern */
font-family: 'Arial', sans-serif;                            /* Clean */
font-family: 'Georgia', serif;                               /* Traditional */
font-family: 'Calibri', 'Arial', sans-serif;               /* Corporate */

/* Font Sizes */
.name { font-size: 28-42px; }           /* Main name */
.title { font-size: 16-20px; }          /* Professional title */
.section-title { font-size: 18-24px; }  /* Section headings */
.job-title { font-size: 16-18px; }      /* Job/degree titles */
.company { font-size: 14-16px; }        /* Company/institution */
.description { font-size: 14-15px; }    /* Body text */
.contact-text { font-size: 13-14px; }   /* Contact details */
```

### 3. Spacing Standards
```css
/* Container Padding */
.container { padding: 40-60px; }

/* Section Margins */
.section { margin-bottom: 30-40px; }

/* Item Margins */
.experience-item, .education-item { margin-bottom: 20-30px; }
.skill-item { margin-bottom: 12-20px; }

/* Internal Padding */
.section-title { margin-bottom: 20-25px; }
.job-title { margin-bottom: 5-8px; }
.company { margin-bottom: 5-8px; }
.date-range { margin-bottom: 10-15px; }
```

### 4. Text Overflow Protection (CRITICAL)
```css
/* Universal text overflow handling - REQUIRED */
.container * {
    word-wrap: break-word;
    overflow-wrap: break-word;
    hyphens: auto;
    max-width: 100%;
}

/* Force break long URLs and text - REQUIRED */
a, .contact-text, .skill-name, .project-link,
.contact-item, .contact-item *,
[href], [href] * {
    word-break: break-all !important;
    overflow-wrap: break-word !important;
    hyphens: none !important;
    max-width: 100% !important;
    white-space: normal !important;
}

/* Sidebar/container specific - REQUIRED */
.sidebar, .main-content {
    overflow-wrap: break-word;
    word-break: break-word;
    hyphens: auto;
}

.sidebar *, .main-content * {
    max-width: 100%;
    overflow-wrap: break-word;
    word-break: break-word;
}
```

---

## Interactive Features

### 1. Drag and Drop Implementation

#### Required CSS
```css
.section.draggable {
    cursor: grab;
    border-radius: 8px;
    padding: 15px;
    margin: 10px -15px;
    transition: all 0.3s ease;
}

.section.draggable:hover {
    background-color: rgba(0,0,0,0.02);
    transform: translateY(-2px);
    box-shadow: 0 4px 12px rgba(0,0,0,0.1);
}

.section.being-dragged {
    opacity: 0.5;
    transform: rotate(2deg) scale(0.95);
    z-index: 1000;
    box-shadow: 0 8px 25px rgba(0,0,0,0.2);
}

.drag-placeholder {
    height: 4px;
    background: #your-primary-color;
    margin: 10px 0;
    border-radius: 2px;
    opacity: 0;
    transform: scale(0.8);
    transition: all 0.3s ease;
}

.drag-over {
    background-color: rgba(your-primary-color, 0.05);
    border: 2px dashed #your-primary-color;
}
```

#### Required JavaScript Functions
```javascript
// Core drag and drop variables
let draggedElement = null;
let placeholder = null;
let dropPosition = null;
let targetElement = null;

// Required functions (copy from existing templates):
function updateDropPosition(e, target) { /* Implementation */ }
function createPlaceholder() { /* Implementation */ }
function handleDragStart(e) { /* Implementation */ }
function handleDragEnd(e) { /* Implementation */ }
function handleDragEnter(e) { /* Implementation */ }
function handleDragOver(e) { /* Implementation */ }
function handleDragLeave(e) { /* Implementation */ }
function handleDrop(e) { /* Implementation */ }

// Initialize drag and drop
document.addEventListener('DOMContentLoaded', function() {
    const draggableElements = document.querySelectorAll('.draggable');

    draggableElements.forEach(element => {
        element.draggable = true;
        element.addEventListener('dragstart', handleDragStart);
        element.addEventListener('dragend', handleDragEnd);
        element.addEventListener('dragenter', handleDragEnter);
        element.addEventListener('dragover', handleDragOver);
        element.addEventListener('dragleave', handleDragLeave);
        element.addEventListener('drop', handleDrop);
    });
});
```

### 2. Content Editability
```html
<!-- All user-visible text should be editable -->
<h1 class="name" contenteditable="true">Default Name</h1>
<p class="title" contenteditable="true">Default Title</p>
<div class="contact-text" contenteditable="true">default@email.com</div>
<h3 class="job-title" contenteditable="true">Default Job Title</h3>
<p class="description" contenteditable="true">Default description</p>
```

### 3. Empty Section Handling
```javascript
// Hide empty sections
function hideEmptySections() {
    const sections = document.querySelectorAll('.section[data-section]');
    sections.forEach(section => {
        const sectionType = section.getAttribute('data-section');
        let isEmpty = false;

        if (sectionType === 'skills') {
            const skillNames = section.querySelectorAll('.skill-name');
            isEmpty = Array.from(skillNames).every(skill => !skill.textContent.trim());
        } else if (sectionType === 'experience') {
            const jobTitles = section.querySelectorAll('.job-title');
            isEmpty = Array.from(jobTitles).every(title => !title.textContent.trim());
        } else if (sectionType === 'education') {
            const degreeTitles = section.querySelectorAll('.degree-title');
            isEmpty = Array.from(degreeTitles).every(title => !title.textContent.trim());
        } else if (sectionType === 'summary') {
            const summaryText = section.querySelector('.summary-text');
            isEmpty = !summaryText || !summaryText.textContent.trim();
        }

        section.style.display = isEmpty ? 'none' : 'block';
    });
}

// Check for empty sections on load and content change
document.addEventListener('DOMContentLoaded', hideEmptySections);
document.addEventListener('input', hideEmptySections);
```

---

## Data Rendering Implementation

### 1. Main Render Function
```javascript
function renderResumeData(data) {
    console.log("Rendering data:", data);

    // Handle missing data structure
    if (!data) {
        console.error("No data provided to renderResumeData");
        return;
    }

    // Ensure we have sections object
    if (!data.sections) {
        data.sections = {};
    }

    // Move common data into sections for consistent rendering
    if (data.skills && !data.sections.skills) {
        data.sections.skills = data.skills;
    }
    if (data.workHistory && !data.sections.workExperience) {
        data.sections.workExperience = data.workHistory;
    } else if (data.workExperience && !data.sections.workExperience) {
        data.sections.workExperience = data.workExperience;
    }
    if (data.education && !data.sections.education) {
        data.sections.education = data.education;
    }

    // Ensure personalInfo exists
    if (!data.personalInfo) {
        data.personalInfo = {};
    }

    // Render personal information
    if (data.personalInfo.fullName) {
        const nameElement = document.querySelector('.name');
        if (nameElement) nameElement.textContent = data.personalInfo.fullName;
    }
    if (data.personalInfo.professionalTitle) {
        const titleElement = document.querySelector('.title');
        if (titleElement) titleElement.textContent = data.personalInfo.professionalTitle;
    }
    if (data.personalInfo.professionalSummary) {
        const summaryElement = document.querySelector('.summary-text');
        if (summaryElement) summaryElement.textContent = data.personalInfo.professionalSummary;
    }

    // Render contact information
    renderContactInfo(data.personalInfo);

    // Render all sections
    renderSections(data.sections);

    // Hide empty sections after rendering
    hideEmptySections();
}
```

### 2. Contact Information Rendering
```javascript
function renderContactInfo(personalInfo) {
    // Template 6 & 7 style (with .contact-text elements)
    const contactItems = document.querySelectorAll('.contact-text');
    if (contactItems.length >= 4) {
        if (personalInfo.email) contactItems[0].textContent = personalInfo.email;
        if (personalInfo.phone) contactItems[1].textContent = personalInfo.phone;
        if (personalInfo.address) contactItems[2].textContent = personalInfo.address;
        if (personalInfo.linkedin) contactItems[3].textContent = personalInfo.linkedin;
    }

    // Template 8, 9, 10 style (direct .contact-item elements)
    // Use appropriate selector based on your template:
    // const contactItems = document.querySelectorAll('.header .contact-item');
    // const contactItems = document.querySelectorAll('.header-right .contact-item');
}
```

### 3. Skills Rendering
```javascript
function renderSkills(skills) {
    const skillsContainer = document.querySelector('.section[data-section="skills"]');
    if (!skillsContainer) return;

    // Remove existing skill items but keep the section title
    const existingSkills = skillsContainer.querySelectorAll('.skill-item');
    existingSkills.forEach(skill => skill.remove());

    if (skills && skills.length > 0) {
        skills.forEach(skill => {
            const skillElement = document.createElement('div');
            skillElement.className = 'skill-item';

            // Use correct proficiency field
            const percentage = proficiencyToPercentage(
                skill.proficiencyLevel || skill.proficiency || skill.level || 5
            );

            skillElement.innerHTML = `
                <div class="skill-name" contenteditable="true">${skill.name || ''}</div>
                <div class="skill-bar">
                    <div class="skill-progress" style="width: ${percentage}%"></div>
                </div>
                <div class="skill-percentage">${percentage}%</div>
            `;

            skillsContainer.appendChild(skillElement);
        });
        skillsContainer.style.display = 'block';
    } else {
        skillsContainer.style.display = 'none';
    }
}

function proficiencyToPercentage(level, maxLevel = 10) {
    return (level / maxLevel) * 100;
}
```

### 4. Work Experience Rendering
```javascript
function renderWorkExperience(experiences) {
    const experienceContainer = document.querySelector('.section[data-section="experience"]');
    if (!experienceContainer) return;

    // Remove existing experience items but keep the section title
    const existingExperiences = experienceContainer.querySelectorAll('.experience-item');
    existingExperiences.forEach(exp => exp.remove());

    if (experiences && experiences.length > 0) {
        experiences.forEach(exp => {
            const expElement = document.createElement('div');
            expElement.className = 'experience-item';

            // Handle current job properly
            const endDate = exp.current || exp.isCurrent ? 'Present' : (exp.endDate || '');

            expElement.innerHTML = `
                <h3 class="job-title" contenteditable="true">${exp.position || exp.title || ''}</h3>
                <div class="company" contenteditable="true">${exp.company || ''}</div>
                <div class="date-range" contenteditable="true">${exp.startDate || ''} - ${endDate}</div>
                <p class="description" contenteditable="true">${exp.description || ''}</p>
            `;

            experienceContainer.appendChild(expElement);
        });
        experienceContainer.style.display = 'block';
    } else {
        experienceContainer.style.display = 'none';
    }
}
```

### 5. Education Rendering
```javascript
function renderEducation(educations) {
    const educationContainer = document.querySelector('.section[data-section="education"]');
    if (!educationContainer) return;

    // Remove existing education items but keep the section title
    const existingEducations = educationContainer.querySelectorAll('.education-item');
    existingEducations.forEach(edu => edu.remove());

    if (educations && educations.length > 0) {
        educations.forEach(edu => {
            const eduElement = document.createElement('div');
            eduElement.className = 'education-item';

            // Combine degree and field of study
            const degreeText = edu.degree && edu.fieldOfStudy ?
                `${edu.degree} in ${edu.fieldOfStudy}` :
                (edu.degree || edu.title || '');

            // Use year fields primarily
            const dateRange = edu.startYear && edu.endYear ?
                `${edu.startYear} - ${edu.endYear}` :
                `${edu.startDate || ''} - ${edu.endDate || ''}`;

            eduElement.innerHTML = `
                <h3 class="degree-title" contenteditable="true">${degreeText}</h3>
                <div class="institution" contenteditable="true">${edu.university || edu.institution || edu.school || ''}</div>
                <div class="date-range" contenteditable="true">${dateRange}</div>
                <p class="description" contenteditable="true">${edu.gpa ? `GPA: ${edu.gpa}` : (edu.description || '')}</p>
            `;

            educationContainer.appendChild(eduElement);
        });
        educationContainer.style.display = 'block';
    } else {
        educationContainer.style.display = 'none';
    }
}
```

### 6. Section Router
```javascript
function renderSections(sections) {
    for (const [sectionKey, sectionData] of Object.entries(sections)) {
        switch (sectionKey) {
            case 'skills':
                renderSkills(sectionData);
                break;
            case 'workExperience':
                renderWorkExperience(sectionData);
                break;
            case 'education':
                renderEducation(sectionData);
                break;
            case 'certifications':
                renderCertifications(sectionData);
                break;
            case 'languages':
                renderLanguages(sectionData);
                break;
            case 'accomplishments':
                renderAccomplishments(sectionData);
                break;
            case 'interests':
                renderInterests(sectionData);
                break;
            default:
                console.log(`Custom section ${sectionKey} not implemented in this template`);
                break;
        }
    }
}

// Placeholder functions for optional sections
function renderCertifications(certifications) {
    console.log(`Certifications section not available in this template`);
}

function renderLanguages(languages) {
    console.log(`Languages section not available in this template`);
}

function renderAccomplishments(accomplishments) {
    console.log(`Accomplishments section not available in this template`);
}

function renderInterests(interests) {
    console.log(`Interests section not available in this template`);
}
```

### 7. Drag and Drop Re-initialization
```javascript
// Re-initialize drag and drop after data is loaded
window.addEventListener("message", function (event) {
    if (event.data && event.data.type === "RESUME_DATA") {
        setTimeout(() => {
            const draggableElements = document.querySelectorAll('.draggable');
            draggableElements.forEach(element => {
                element.draggable = true;
                element.addEventListener('dragstart', handleDragStart);
                element.addEventListener('dragend', handleDragEnd);
                element.addEventListener('dragenter', handleDragEnter);
                element.addEventListener('dragover', handleDragOver);
                element.addEventListener('dragleave', handleDragLeave);
                element.addEventListener('drop', handleDrop);
            });
        }, 100);
    }
});
```

---

## Responsive Design

### 1. Mobile Breakpoints
```css
/* Tablet and below */
@media (max-width: 768px) {
    .container {
        margin: 10px;
        padding: 25px 20px;
    }

    /* Convert two-column to single column */
    .container {
        grid-template-columns: 1fr !important;
    }

    /* Adjust font sizes */
    .name { font-size: 24px; }
    .section-title { font-size: 18px; }

    /* Stack contact info vertically */
    .contact-info {
        flex-direction: column;
        gap: 15px;
    }
}

/* Mobile */
@media (max-width: 480px) {
    .container {
        margin: 5px;
        padding: 20px 15px;
    }

    .name { font-size: 20px; }
    .section-title { font-size: 16px; }
}
```

### 2. Print Optimization
```css
@media print {
    body {
        background: white;
        margin: 0;
        padding: 0;
    }

    .container {
        box-shadow: none;
        margin: 0;
        max-width: none;
        width: 100%;
        min-height: auto;
    }

    /* Disable hover effects */
    .section.draggable:hover {
        background-color: transparent;
        transform: none;
        box-shadow: none;
    }

    /* Ensure proper page breaks */
    .section {
        page-break-inside: avoid;
    }

    .experience-item, .education-item {
        page-break-inside: avoid;
    }
}
```

---

## Testing & Validation

### 1. Test Data Structure
Create a test file to validate your template:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Template Test</title>
</head>
<body>
    <iframe id="templateFrame" src="your-template.html" width="100%" height="600px"></iframe>

    <script>
        const testData = {
            type: "RESUME_DATA",
            payload: {
                personalInfo: {
                    fullName: "Test User Name",
                    professionalTitle: "Test Job Title",
                    email: "test@example.com",
                    phone: "+1-555-123-4567",
                    address: "Test City, State",
                    linkedin: "https://linkedin.com/in/testuser",
                    professionalSummary: "Test professional summary text."
                },
                skills: [
                    { name: "JavaScript", proficiencyLevel: 9 },
                    { name: "Python", proficiencyLevel: 8 }
                ],
                workHistory: [
                    {
                        position: "Senior Developer",
                        company: "Test Company",
                        startDate: "2020-01",
                        endDate: "",
                        current: true,
                        description: "Test job description."
                    }
                ],
                education: [
                    {
                        degree: "Bachelor of Science",
                        fieldOfStudy: "Computer Science",
                        university: "Test University",
                        startYear: "2014",
                        endYear: "2018",
                        gpa: "3.8/4.0"
                    }
                ]
            }
        };

        setTimeout(() => {
            document.getElementById('templateFrame').contentWindow.postMessage(testData, '*');
        }, 1000);
    </script>
</body>
</html>
```

### 2. Validation Checklist

#### Visual Validation
- [ ] Name displays correctly from `fullName`
- [ ] Title displays correctly from `professionalTitle`
- [ ] Summary displays correctly from `professionalSummary`
- [ ] Address displays correctly from `address` (not `location`)
- [ ] Skills show correct proficiency from `proficiencyLevel`
- [ ] Current job shows "Present" when `current: true`
- [ ] Education combines degree and field of study
- [ ] GPA displays when available
- [ ] Long URLs break properly without overflow
- [ ] Empty sections are hidden

#### Functional Validation
- [ ] Drag and drop works within containers
- [ ] Sections can be reordered
- [ ] Content is editable
- [ ] PostMessage listener responds
- [ ] Console shows data processing logs
- [ ] Mobile layout works properly
- [ ] Print layout is clean

#### Technical Validation
- [ ] No external dependencies
- [ ] All CSS is inline
- [ ] All JavaScript is inline
- [ ] No console errors
- [ ] Proper semantic HTML
- [ ] Accessibility attributes present

---

## Best Practices

### 1. Performance
- **Minimize DOM manipulation** - Batch updates when possible
- **Use CSS transforms** - For animations instead of changing layout properties
- **Optimize selectors** - Use specific selectors to avoid unnecessary queries
- **Debounce events** - For resize and input handlers

### 2. Accessibility
```html
<!-- Use semantic HTML -->
<main class="container">
    <header class="profile-section">
        <h1 class="name">Name</h1>
    </header>
    <section class="section" data-section="experience">
        <h2 class="section-title">Experience</h2>
        <article class="experience-item">
            <h3 class="job-title">Job Title</h3>
        </article>
    </section>
</main>

<!-- Add ARIA labels where helpful -->
<div class="skill-bar" role="progressbar" aria-valuenow="80" aria-valuemin="0" aria-valuemax="100">
    <div class="skill-progress" style="width: 80%"></div>
</div>
```

### 3. Code Organization
```javascript
// Group related functions
// 1. Data rendering functions
// 2. Drag and drop functions
// 3. Utility functions
// 4. Event listeners

// Use consistent naming
function renderSkills() { }      // render + SectionName
function handleDragStart() { }   // handle + EventName
function hideEmptySections() { } // action + Description
```

### 4. Error Handling
```javascript
function renderResumeData(data) {
    try {
        if (!data) {
            console.error("No data provided");
            return;
        }

        // Rendering logic with null checks
        if (data.personalInfo?.fullName) {
            // Safe property access
        }

    } catch (error) {
        console.error("Error rendering resume data:", error);
    }
}
```

---

## Common Pitfalls

### 1. Data Field Mismatches
```javascript
// ❌ WRONG - Old field names
data.personalInfo.name           // Should be fullName
data.personalInfo.title          // Should be professionalTitle
data.personalInfo.summary        // Should be professionalSummary
data.personalInfo.location       // Should be address
data.workExperience             // Should be workHistory
skill.proficiency               // Should be proficiencyLevel

// ✅ CORRECT - Current field names
data.personalInfo.fullName
data.personalInfo.professionalTitle
data.personalInfo.professionalSummary
data.personalInfo.address
data.workHistory
skill.proficiencyLevel
```

### 2. Text Overflow Issues
```css
/* ❌ WRONG - Insufficient overflow protection */
.contact-text {
    overflow: hidden;
    text-overflow: ellipsis;
}

/* ✅ CORRECT - Comprehensive overflow protection */
.contact-text {
    word-wrap: break-word;
    overflow-wrap: break-word;
    word-break: break-all;
    max-width: 100%;
    white-space: normal;
    hyphens: none;
}
```

### 3. Drag and Drop Container Issues
```javascript
// ❌ WRONG - Allowing cross-container dragging
function handleDragEnter(e) {
    // No container validation
}

// ✅ CORRECT - Restrict to same container
function handleDragEnter(e) {
    const draggedContainer = draggedElement.closest('.sidebar, .main-content');
    const targetContainer = this.closest('.sidebar, .main-content');

    if (draggedContainer !== targetContainer) {
        return; // Prevent cross-container drops
    }
}
```

### 4. Missing Data Validation
```javascript
// ❌ WRONG - No validation
function renderSkills(skills) {
    skills.forEach(skill => {
        // Will crash if skills is null/undefined
    });
}

// ✅ CORRECT - Proper validation
function renderSkills(skills) {
    if (!skills || !Array.isArray(skills) || skills.length === 0) {
        skillsContainer.style.display = 'none';
        return;
    }

    skills.forEach(skill => {
        // Safe to process
    });
}
```

### 5. Incorrect DOM Manipulation
```javascript
// ❌ WRONG - Clearing entire container
skillsContent.innerHTML = '';

// ✅ CORRECT - Selective removal
const existingSkills = skillsContainer.querySelectorAll('.skill-item');
existingSkills.forEach(skill => skill.remove());
```

### 6. Missing Re-initialization
```javascript
// ❌ WRONG - Drag and drop breaks after data load
// No re-initialization after postMessage

// ✅ CORRECT - Re-attach event listeners
window.addEventListener("message", function (event) {
    if (event.data && event.data.type === "RESUME_DATA") {
        setTimeout(() => {
            // Re-initialize drag and drop
            initializeDragAndDrop();
        }, 100);
    }
});
```

---

## Template Examples

### Basic Template Structure
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Professional Resume Template</title>
    <style>
        /* All your CSS here */
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1 class="name" contenteditable="true">Your Name</h1>
            <p class="title" contenteditable="true">Your Title</p>
        </div>

        <div class="content">
            <div class="section draggable" data-section="summary">
                <h2 class="section-title">Professional Summary</h2>
                <p class="summary-text" contenteditable="true">Your summary here</p>
            </div>

            <div class="section draggable" data-section="experience">
                <h2 class="section-title">Work Experience</h2>
                <!-- Experience items will be dynamically generated -->
            </div>

            <div class="section draggable" data-section="education">
                <h2 class="section-title">Education</h2>
                <!-- Education items will be dynamically generated -->
            </div>

            <div class="section draggable" data-section="skills">
                <h2 class="section-title">Skills</h2>
                <!-- Skill items will be dynamically generated -->
            </div>
        </div>
    </div>

    <script>
        /* All your JavaScript here */
    </script>
</body>
</html>
```

---

## Conclusion

Following these guidelines ensures your resume templates will:

1. **Integrate seamlessly** with the resume builder system
2. **Handle data correctly** using the proper field mappings
3. **Provide professional appearance** suitable for job applications
4. **Work across all devices** with responsive design
5. **Support user interaction** with drag-and-drop and editing
6. **Handle edge cases** gracefully with proper validation

Remember to test thoroughly with the actual data payload structure and validate both visual appearance and functional behavior before considering a template complete.

For questions or clarifications on any aspect of template development, refer to the existing templates (template6.html through template10.html) as working examples that implement all these guidelines correctly.
