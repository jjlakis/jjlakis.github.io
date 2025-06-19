---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: splash
title: We are experts in transformation
excerpt: For over 11 years, we've been transforming small, medium and large businesses by providing infrastructure and software development solutions.
header:
  overlay_image: /img/dc3.png
  actions:
    - label: "Case studies"
      url: "/cases"
    - label: "Our services"
      url: "/services"
    - label: "Blog"
      url: "/posts"
feature_technologies:
  - title: "Technologies"
    excerpt: 'Your content with **Markdown** formatting. Centered with `type="center"`'
technologies_gallery:
  - image_path: /img/tech/azure.svg
    alt: "Azure"
  - image_path: /img/tech/aws.png
    alt: "AWS"
  - image_path: /img/tech/gcp.png
    alt: "GCP"
  - image_path: /img/tech/Kubernetes_logo.svg
    alt: "Kubernetes"
  - image_path: /img/tech/tf.svg
    alt: "Terraform"
  - image_path: /img/tech/Logo_of_OpenTofu.svg.png
    alt: "OpenTofu"
  - image_path: /img/tech/flux.png
    alt: "Flux"
  - image_path: /img/tech/GitHub Actions.png
    alt: "Github actions"
    title: "Image 1 title caption"
  - image_path: /img/tech/prometheus.png
    alt: "Prometheus"
---

<style>
/* Force 3-column grid layout */
.tech-gallery {
  display: grid !important;
  grid-template-columns: repeat(3, 1fr) !important;
  gap: 1em !important;
}

/* Mobile responsive - 2 columns on smaller screens */
@media (max-width: 768px) {
  .tech-gallery {
    grid-template-columns: repeat(2, 1fr) !important;
  }
}

/* Very small screens - single column */
@media (max-width: 480px) {
  .tech-gallery {
    grid-template-columns: 1fr !important;
  }
}

.tech-gallery figure,
.tech-gallery .gallery__item {
  margin: 0 !important;
  text-align: center !important;
}

.tech-gallery figure img,
.tech-gallery .gallery__item img,
figure.tech-gallery img {
  width: 100% !important;
  height: 150px !important;
  object-fit: contain !important;
  object-position: center !important;
  background-color: #f8f9fa !important;
  border: 1px solid #e9ecef !important;
  border-radius: 8px !important;
  padding: 10px !important;
  box-sizing: border-box !important;
  margin: 0 !important;
  display: block !important;
}
</style>

<p style="text-align: center; font-weight: bold">Technologies</p>
{% include gallery id="technologies_gallery" class="tech-gallery" %}
