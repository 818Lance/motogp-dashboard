# AGENTS.md — Kauaʻi Landfill Governance Dashboard (Procurement Tracker)
    
    ## Mission
    Maintain a procurement tracker for Kauaʻi solid waste + Kekaha landfill related procurements
    (RFP/IFB/PROF/Exempt). Output must drop cleanly into a dashboard with filtering.
    
    ## Ground rules
    - Do NOT invent facts (vendors, amounts, dates, status). If unknown, leave null/blank and set stage=Unknown.
    - Preserve original IDs and URLs exactly as provided.
    - Prefer deterministic transforms over “smart guessing.”
    - If you add new items, include at least one source URL and a short evidence note.
    
    ## Output formats (required)
    - data/procurement_tracker.csv
    - data/procurement_tracker.json
    Both must include computed fields: `stage` and `next_action`.
    
    ## Schema (required columns)
    tracker_id, project_tag, solicitation_id, procurement_type, title, issuing_agency, portal,
    status, stage, next_action, posted_date, due_date, award_date, contract_number,
    awarded_vendor, est_value_usd, contract_term, package_download_url, related_links,
    notes, last_checked
    
    ## Computed field rules
    ### stage normalization (from status)
    - If status contains: "Award" (Awarded/NOA/Contract Awarded/Notice of Award) → stage="Awarded"
    - If status contains: "Exempt" or "Exemption" → stage="Exempt"
    - If status contains: "Open" or "Posted" or "Accepting" → stage="Open"
    - If status contains: "Closed" or "Bid Closed" or "Due Date Passed" or "Cancelled" → stage="Closed"
    - Else → stage="Unknown"
    
    ### next_action
    - stage="Awarded":
      - "Pull executed contract & full SOW; add milestones/deliverables; monitor amendments"
      - If vendor/amount missing: "Pull award detail (vendor/amount/scope) and update tracker"
    - stage="Closed":
      - "Confirm outcome (award/cancel/re-solicit); pull NOA/contract or closeout note"
    - stage="Open":
      - "Monitor addenda; capture Q&A; set reminders for due date; verify submission portal"
    - stage="Exempt":
      - "Capture vendor/PO + unit pricing; track follow-on competitive solicitation; audit justification"
    - stage="Unknown":
      - "Confirm status in source portal; update status/stage; attach evidence link"
    
    ## Quality checks (required before finishing)
    - CSV parses cleanly (no broken quotes; related_links can be semicolon-delimited).
    - JSON is valid; related_links is an array.
    - last_checked set to today (YYYY-MM-DD).
    - No duplicate tracker_id.
