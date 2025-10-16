# Google Ads MCP Plugin Roadmap

**Last Updated:** October 2025
**Status:** Active Development

This document tracks planned features, technical considerations, and implementation priorities for the google-ads-mcp plugin.

---

## Current Status (v1.0.0)

### Implemented Features
- ✅ Account Information Skill (account details, MCC hierarchies)
- ✅ Campaign Performance Skill (metrics, conversions, cost analysis)
- ✅ GAQL Query Builder Skill (query construction, documentation access)
- ✅ Account alias support (accounts.json template)
- ✅ Automatic data formatting (micros, percentages)

---

## Planned Features

### Priority 1: Near-Term Development (Next 1-2 Months)

#### 1. Budget Pacing Calculator Skill ⭐ HIGH PRIORITY
**Status:** Planned
**Target:** November 2025
**Rationale:** Practical daily use, high community value, straightforward implementation

**Features:**
- Daily budget pacing calculation
- Monthly budget tracking and forecasting
- Over/under-pacing alerts
- End-of-period spend projections
- Campaign-level and account-level views

**Technical Implementation:**
- Query metrics by day using segments.date
- Calculate average daily spend
- Compare to budget targets (from campaign settings)
- Project remaining budget based on current pace
- Alert thresholds (e.g., >10% over/under pace)

**GAQL Queries Needed:**
```sql
-- Daily spend by campaign
SELECT
  campaign.name,
  campaign.id,
  segments.date,
  metrics.cost_micros,
  campaign_budget.amount_micros
FROM campaign
WHERE segments.date DURING THIS_MONTH

-- Budget settings
SELECT
  campaign.name,
  campaign_budget.amount_micros,
  campaign_budget.period
FROM campaign
```

**User Stories:**
- "How is my budget pacing this month?"
- "Am I on track to hit my monthly spend target?"
- "Which campaigns are under-spending?"

---

#### 2. Black Friday / Seasonal Campaign Agent ⭐ HIGH PRIORITY (TIMELY)
**Status:** Planned
**Target:** Late October 2025 (before Black Friday)
**Rationale:** Timely (mid-Oct now), high engagement period, practical value

**Features:**
- Pre-Black Friday campaign audit
- Budget scaling recommendations
- Historical performance comparison (YoY)
- Seasonal keyword suggestions
- Ad copy checks for promotional messaging
- Inventory/stock availability reminders
- Post-event performance analysis

**Technical Implementation:**
- Compare current period vs. same period last year
- Identify top-performing campaigns from previous BFCM
- Check for seasonal keywords in campaigns
- Validate budget sufficiency based on historical data
- Monitor impression share for key campaigns

**GAQL Queries Needed:**
```sql
-- YoY comparison
SELECT
  campaign.name,
  segments.date,
  metrics.impressions,
  metrics.clicks,
  metrics.conversions,
  metrics.cost_micros
FROM campaign
WHERE segments.date BETWEEN 'YYYY-11-20' AND 'YYYY-12-05'

-- Current performance baseline
SELECT
  campaign.name,
  metrics.average_daily_spend,
  metrics.search_impression_share
FROM campaign
WHERE segments.date DURING LAST_30_DAYS
```

**Agent Workflow:**
1. Gather historical BFCM data (previous 1-2 years)
2. Analyze current campaign setup
3. Calculate recommended budget increase
4. Identify gaps (missing seasonal keywords, etc.)
5. Generate preparation checklist
6. Create monitoring plan for event period

**User Stories:**
- "Prepare my account for Black Friday"
- "What should I change before BFCM?"
- "How did last year's Black Friday campaigns perform?"
- "Am I ready for the holiday shopping season?"

**Deliverables:**
- Pre-event audit report
- Recommended changes checklist
- Budget scaling calculator
- Monitoring dashboard setup
- Post-event analysis template

---

#### 3. /ads-snapshot Command ⭐ HIGH PRIORITY
**Status:** Planned
**Target:** November 2025
**Rationale:** Quick daily check, simple implementation, immediate value

**Features:**
- Quick performance overview (7/30/90 day options)
- Top 5 campaigns by spend/conversions
- Account totals (spend, conversions, ROAS)
- Unusual pattern alerts (spend spikes, conversion drops)
- One-command dashboard

**Technical Implementation:**
- Single command with optional period parameter
- Formatted table output
- Color coding for alerts (if supported)
- Export option to share with team

**Usage:**
```
/ads-snapshot              # Default: last 7 days
/ads-snapshot 30           # Last 30 days
/ads-snapshot today        # Today only
```

---

### Priority 2: Medium-Term Development (2-4 Months)

#### 4. Scripts to Sheets Integration Skill
**Status:** Planned
**Target:** December 2025
**Rationale:** Bridges existing 8020agent products, high community value

**Features:**
- Connect Google Sheets data from scripts
- Analyze sheets output with AI
- Generate insights from script reports
- Automate recommendations based on sheet data
- Bridge legacy Scripts & Sheets Mastery course content to AI

**Technical Considerations:**
- May need additional MCP server for Google Sheets API
- Or: instruct users to export sheets as CSV and read locally
- Integration with existing Performance Max script outputs
- Works with community members' existing workflows

**User Stories:**
- "Analyze my Performance Max script output"
- "What insights can you find in this sheet?"
- "Compare this week's script data to last week"

---

#### 5. Conversion Tracking Debugger Skill
**Status:** Planned
**Target:** Q1 2026
**Rationale:** Technical but high-value, helps troubleshoot common issues

**Features:**
- Validate conversion action setup
- Check tracking code implementation
- Identify attribution issues
- Compare conversion counts across sources (GA4 vs Ads)
- Diagnose common tracking problems

**Technical Implementation:**
- Query conversion_action resource
- Check conversion settings (attribution model, counting method)
- Analyze conversion lag data
- Compare conversion metrics across time periods
- Provide troubleshooting steps

**GAQL Queries Needed:**
```sql
-- Conversion action settings
SELECT
  conversion_action.name,
  conversion_action.id,
  conversion_action.status,
  conversion_action.type,
  conversion_action.category,
  conversion_action.counting_type,
  conversion_action.attribution_model_settings.attribution_model
FROM conversion_action

-- Conversion performance
SELECT
  conversion_action.name,
  metrics.conversions,
  metrics.all_conversions,
  metrics.view_through_conversions
FROM conversion_action
WHERE segments.date DURING LAST_30_DAYS
```

**Complexity:** Medium-High (requires understanding of tracking setup)

---

#### 6. Quality Score Analyzer Agent
**Status:** Planned
**Target:** Q1 2026
**Rationale:** Deep expertise showcase, differentiates from basic tools

**Features:**
- Keyword-level quality score analysis
- Component breakdown (expected CTR, ad relevance, landing page)
- Comparison to account averages
- Improvement recommendations
- Historical quality score trends

**Technical Implementation:**
- Query quality_score_history view
- Analyze QS components
- Identify patterns in low QS keywords
- Provide specific improvement actions

**User Stories:**
- "Why are my quality scores low?"
- "Which keywords should I focus on improving?"
- "How has my quality score changed over time?"

---

#### 7. /ads-compare Command
**Status:** Planned
**Target:** Q1 2026
**Rationale:** Common analysis task, straightforward implementation

**Features:**
- Compare two time periods
- Show deltas in all key metrics
- Highlight significant changes (>20% variance)
- Formatted comparison table

**Usage:**
```
/ads-compare last-week this-week
/ads-compare last-month this-month
/ads-compare 2024-10-01:2024-10-07 2024-10-08:2024-10-14
```

---

### Priority 3: Future Development (4+ Months)

#### 8. Performance Max Auditor Agent ⚠️ BLOCKED
**Status:** On Hold
**Blocker:** Waiting for PMAX channel report in next API version
**Target:** TBD (dependent on Google API release)
**Rationale:** Popular topic (Mike's PMax script history), but technically complex

**Current Challenges:**
- Requires combining 4+ different GAQL queries
- Logic is complex with assumptions
- Reports don't always match UI data
- Asset group performance data incomplete

**Future Implementation (when API available):**
- Use new PMAX channel report endpoint
- Analyze asset group performance
- Audit campaign structure
- Recommend optimizations
- Generate audit report

**What to Monitor:**
- Google Ads API release notes
- PMAX channel report availability
- Beta access opportunities

**User Stories (deferred):**
- "Audit my Performance Max campaigns"
- "What's wrong with my PMax setup?"
- "How are my asset groups performing?"

---

#### 9. Competitor Analysis Agent ⚠️ COMPLEX
**Status:** Backlog
**Target:** Q2 2026 (requires additional tooling)
**Rationale:** Strategic value, but API limitations

**Challenges:**
- Auction Insights NOT available via API
- Would require manual data input (URLs, screenshots)
- Need Chrome DevTools MCP integration for competitor research
- User would need to provide 4-5 competitor URLs

**Possible Approach:**
- Package Chrome DevTools MCP for screenshot capture
- User provides competitor landing pages
- Agent analyzes competitor ad copy (from manual input)
- Strategic positioning recommendations
- Market share estimation (without auction insights)

**Dependencies:**
- Chrome DevTools MCP server setup
- Manual data collection workflow
- User training on gathering competitor data

**Decision:** Defer until Chrome DevTools MCP integration is mature

---

#### 10. Shopping Campaign Analyzer Skill
**Status:** Backlog
**Target:** Q2 2026
**Rationale:** Niche but valuable for e-commerce advertisers

**Features:**
- Product-level performance analysis
- Merchant Center feed issues
- Product group optimization
- Bidding recommendations

---

#### 11. Ad Copy Analyzer Skill
**Status:** Backlog
**Target:** Q2 2026
**Rationale:** Creative analysis, pattern recognition

**Features:**
- Review ad text for best practices
- Compare CTR across different copy styles
- Suggest improvements based on performance
- A/B test recommendations

---

### Automation Features (Lower Priority)

#### 12. Daily Report Hook
**Status:** Backlog
**Target:** Q3 2026
**Rationale:** Automation, requires integration setup

**Features:**
- Auto-generate morning performance summary
- Post to Slack/email
- Scheduled trigger
- Customizable metrics

---

#### 13. Alert Monitor Hook
**Status:** Backlog
**Target:** Q3 2026
**Rationale:** Proactive monitoring, agency use case

**Features:**
- Watch for critical changes
- Trigger notifications
- Auto-create tickets
- Threshold-based alerts

---

## Technical Considerations

### API Limitations to Track
1. **PMAX Channel Report:** Not yet available (impacts #8)
2. **Auction Insights:** Not available via API (impacts #9)
3. **Real-time Data:** API has delay vs. UI
4. **Rate Limits:** Consider query volume for agents
5. **Historical Data:** Some metrics limited to specific time ranges

### MCP Server Dependencies
- **Current:** google_ads_mcp (working)
- **Future Needs:**
  - Google Sheets MCP (for #4)
  - Chrome DevTools MCP (for #9)
  - Slack/Email MCP (for #12, #13)

### Community Considerations
- Most members are small agencies or freelancers
- Practical > theoretical tools
- Time-saving > comprehensive
- Bridge to existing 8020agent products
- Support Scripts & Sheets Mastery course content

---

## Development Workflow

### For Each New Feature:
1. **Spec:** Document in this file with user stories
2. **Prototype:** Build locally, test with real accounts
3. **Test:** Share with beta users in community
4. **Iterate:** Refine based on feedback
5. **Document:** Update plugin README
6. **Release:** Version bump, announce to community
7. **Support:** Monitor issues, gather feedback

### Version Numbering
- **1.x.x:** Skills only
- **2.x.x:** Skills + Commands
- **3.x.x:** Skills + Commands + Agents
- **4.x.x:** Skills + Commands + Agents + Hooks

---

## Community Feedback Loop

### Beta Testing Program
- Recruit 5-10 active community members
- Early access to new features
- Feedback via dedicated Slack/Circle channel
- Monthly iteration cycles

### Feature Requests
- Track in GitHub Issues
- Tag with priority labels
- Community voting on features
- Quarterly roadmap reviews

---

## Success Metrics

### Plugin Adoption
- Installations from marketplace
- Active monthly users
- Feature usage analytics (if possible)

### Community Value
- Support tickets reduced
- Time saved (user feedback)
- Course completion rates (Scripts & Sheets integration)
- Retention impact on Ads to AI membership

---

## Next Actions

### Immediate (This Week)
1. ✅ Publish v1.0.0 to GitHub
2. ✅ Create this roadmap
3. ⬜ Announce to community
4. ⬜ Start Budget Pacing Skill (Priority 1, #1)

### Short-term (Next 2 Weeks)
1. ⬜ Build Black Friday Agent (Priority 1, #2) - TIMELY
2. ⬜ Build /ads-snapshot Command (Priority 1, #3)
3. ⬜ Beta test with 3-5 community members
4. ⬜ Gather initial feedback

### Medium-term (Next Month)
1. ⬜ Release v1.1.0 with Budget Pacing + Black Friday
2. ⬜ Monitor community usage
3. ⬜ Plan Scripts to Sheets integration
4. ⬜ Research Chrome DevTools MCP options

---

## Open Questions

1. **Data Privacy:** How do we handle sensitive client data in agent outputs?
2. **Rate Limiting:** What's safe query volume for agents vs. skills?
3. **Error Handling:** How do we gracefully handle API errors/timeouts?
4. **Offline Mode:** Can skills work without active MCP connection?
5. **Multi-Account:** How do agents handle switching between accounts?
6. **Benchmarking:** Where do we get industry benchmark data for comparisons?

---

## Resources

- [Google Ads API Docs](https://developers.google.com/google-ads/api/docs/start)
- [GAQL Reference](https://developers.google.com/google-ads/api/docs/query/overview)
- [Claude Code Skills Guide](https://docs.claude.com/en/docs/claude-code/skills)
- [Claude Code Agents Guide](https://docs.claude.com/en/docs/claude-code/sub-agents)
- [MCP Documentation](https://docs.claude.com/en/docs/claude-code/mcp)

---

**Maintained by:** Mike Rhodes
**Last Review:** October 2025
**Next Review:** November 2025
