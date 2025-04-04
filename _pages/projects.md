---
layout: page
title: projects
permalink: /projects/
description:
nav: true
nav_order: 3
display_categories: [technical, community engagement]
horizontal: false
---

<!-- pages/projects.md -->
<div class="projects">
{% if site.enable_project_categories and page.display_categories %}
  <!-- Display categorized projects -->
  {% for category in page.display_categories %}
  <div class="category-section">
    <!-- Category header as a clickable link with anchor for scroll and URL update -->
    <a id="{{ category | slugify }}" class="category-toggle-btn" href="#category-{{ category | slugify }}" onclick="toggleCategory('{{ category | slugify }}')">
      <h2 class="category">{{ category }}</h2>
    </a>

    <!-- Collapsible content for each category -->
    <div id="category-{{ category | slugify }}" class="category-content">
      {% assign categorized_projects = site.projects | where: "category", category %}
      {% assign sorted_projects = categorized_projects | sort: "importance" %}

      <!-- Generate cards for each project -->
      {% if page.horizontal %}
      <div class="container">
        <div class="row row-cols-1 row-cols-md-2">
          {% for project in sorted_projects %}
            {% include projects_horizontal.liquid %}
          {% endfor %}
        </div>
      </div>
      {% else %}
      <div class="row row-cols-1 row-cols-md-3">
        {% for project in sorted_projects %}
          {% include projects.liquid %}
        {% endfor %}
      </div>
      {% endif %}
    </div>

  </div>
  {% endfor %}
{% else %}

<!-- Display projects without categories -->

{% assign sorted_projects = site.projects | sort: "importance" %}

  <!-- Generate cards for each project -->

{% if page.horizontal %}

  <div class="container">
    <div class="row row-cols-1 row-cols-md-2">
      {% for project in sorted_projects %}
        {% include projects_horizontal.liquid %}
      {% endfor %}
    </div>
  </div>
  {% else %}
  <div class="row row-cols-1 row-cols-md-3">
    {% for project in sorted_projects %}
      {% include projects.liquid %}
    {% endfor %}
  </div>
  {% endif %}
{% endif %}
</div>

<script>
document.addEventListener("DOMContentLoaded", function () {
  const hash = window.location.hash.substring(1);

  // Handle hash-specific category expansion
  if (hash) {
    const categoryContent = document.getElementById(hash);
    if (categoryContent) {
      categoryContent.style.maxHeight = categoryContent.scrollHeight + "px"; // Expand
      categoryContent.style.opacity = "1"; // Show
      setTimeout(() => {
        categoryContent.scrollIntoView({
          behavior: "smooth",
          block: "start",
        });
      }, 100);
      return; // Stop further execution as specific category is handled
    }
  }
  else{
    // Default behavior: Expand all categories
    const categorySections = document.querySelectorAll(".category-content");
    categorySections.forEach((section) => {
      section.style.maxHeight = section.scrollHeight + "px"; // Expand
      section.style.opacity = "1"; // Show
    });
  }

  
});

// Function to toggle category visibility when clicked
function toggleCategory(categoryId) {
  const content = document.getElementById("category-" + categoryId);
  const isCollapsed = content.style.maxHeight === "0px" || content.style.maxHeight === "";
  if (isCollapsed) {
    content.style.maxHeight = content.scrollHeight + "px"; // Expand
    content.style.opacity = "1"; // Show
  } else {
    content.style.maxHeight = "0px"; // Collapse
    content.style.opacity = "0"; // Hide
  }
}
</script>

<!-- CSS for styling the collapsible categories with enhanced animation -->
<style>
  .category-toggle-btn {
    text-decoration: none;
    color: inherit;
    display: block;
    padding: 1em 0;
    transition: color 0.3s ease-in-out;
  }

  .category-toggle-btn:hover {
    color: #007bff; /* Adjust the hover color as needed */
  }

  .category {
    font-size: 1.5em;
    font-weight: bold;
    margin: 0;
    padding-left: 20px;
    transition: padding-left 0.3s ease-in-out; /* Add a slight shift for dynamic feel */
  }

  .category-content {
    overflow: hidden;
    max-height: 0; /* Initially collapsed */
    opacity: 0; /* Initially hidden */
    padding: 0 20px;
    margin-top: 10px;
    border-left: 2px solid #ddd;
    transition: max-height 0.5s ease-in-out, opacity 0.3s ease-in-out, padding 0.3s ease-in-out; /* Smooth transition on multiple properties */
  }

  .category-content.open {
    padding-top: 15px;
    padding-bottom: 15px;
  }
</style>
