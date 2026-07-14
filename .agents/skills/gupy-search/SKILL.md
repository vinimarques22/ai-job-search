---
name: gupy-search
description: Search and analyze job postings on Gupy.io (portal.gupy.io) in Brazil. Use this skill when looking for jobs on Gupy or parsing Gupy job descriptions.
---

# Gupy Search Skill

This skill allows you to integrate with the Gupy portal (`portal.gupy.io`) specifically tailored for the Brazilian job market (PT-BR).

## Purpose
- Retrieve job descriptions from Gupy.
- Analyze "Informações Adicionais" or specific screening questions found in the layout.
- Prepare Markdown text blocks for Q&A segments since the candidate's CV is mostly static on the Gupy platform.

## Execution Constraints
- **NO AUTO-APPLY**: Never attempt to automate the browser click/apply subroutines for Gupy.
- **Exception Logic**: When the source URL is `gupy.io`, do NOT optimize the core LaTeX document (`currículo`) aggressively beyond a structural keyword check. Focus heavily on providing the "Informações Adicionais/Respostas" text field optimization blocks as the primary artifact.
- **Method**: Use Jsoup/Requests or fetch raw HTML schema layouts to hit public API endpoints or parse the public job descriptions.
