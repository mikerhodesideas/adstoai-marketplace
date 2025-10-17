# Black Friday Campaign Preparation Agent

## What This Agent Does

The Black Friday Campaign Preparation Agent is a **read-only analysis tool** that helps you prepare your Google Ads campaigns for Black Friday, Cyber Monday, and the holiday shopping season (BFCM).

**Important: This agent NEVER makes changes to your campaigns.** It only reads your historical and current data to provide recommendations. You remain in full control of all decisions.

## Why You Can Trust This Agent

### Read-Only Access
- **No writes**: Cannot modify budgets, bids, or campaign settings
- **No automation**: Cannot pause, enable, or delete campaigns
- **No API mutations**: Only uses Google Ads API `SELECT` queries (read operations)
- **Data analysis only**: Analyzes your data and provides recommendations YOU implement

### Transparency
- All GAQL queries are documented in the agent file
- You can review exactly what data it accesses
- Recommendations are suggestions, not actions
- Full audit trail of what data was analyzed

### Privacy
- Data stays local to your Claude Code session
- No external API calls (uses your configured MCP server)
- No data sent to third parties
- Analysis happens on your machine

## How It Works

### Step-by-Step Process

#### 1. Historical Data Collection
The agent queries your Google Ads account for past Black Friday performance (typically Nov 20 - Dec 5 from previous years):

**Data Retrieved:**
- Campaign names and IDs
- Daily impressions, clicks, conversions
- Daily spend (cost_micros)
- Conversion values
- Average CPC

**GAQL Query Used:**
```sql
SELECT
  campaign.name,
  campaign.id,
  segments.date,
  metrics.impressions,
  metrics.clicks,
  metrics.conversions,
  metrics.conversions_value,
  metrics.cost_micros,
  metrics.average_cpc
FROM campaign
WHERE segments.date BETWEEN 'YYYY-11-20' AND 'YYYY-12-05'
ORDER BY segments.date
```

**Why This Data?**
- Shows which campaigns performed well during previous BFCM periods
- Helps calculate traffic surges (e.g., "traffic was 3x normal")
- Identifies your highest-ROI campaigns for budget prioritization

#### 2. Current Performance Baseline
The agent analyzes your current campaign setup (last 30 days):

**Data Retrieved:**
- Active campaigns and their budgets
- Current spend rates and conversion performance
- Impression share (how much traffic you're capturing)
- Budget lost impression share (traffic lost due to budget limits)
- Ad approval status

**GAQL Query Used:**
```sql
SELECT
  campaign.name,
  campaign.id,
  campaign.status,
  campaign.advertising_channel_type,
  campaign_budget.amount_micros,
  campaign_budget.period,
  metrics.impressions,
  metrics.clicks,
  metrics.conversions,
  metrics.cost_micros,
  metrics.search_impression_share,
  metrics.search_budget_lost_impression_share
FROM campaign
WHERE segments.date DURING LAST_30_DAYS
  AND campaign.status = 'ENABLED'
```

**Why This Data?**
- Establishes "normal" performance baseline
- Identifies campaigns already hitting budget limits
- Shows current ROAS for budget prioritization
- Reveals which campaigns are ready vs. need work

#### 3. Analysis & Calculations

The agent performs these calculations **locally in Claude Code**:

**Traffic Surge Calculation:**
```
BFCM Daily Impressions / Normal Daily Impressions = Traffic Multiplier
Example: 300,000 / 100,000 = 3x traffic surge
```

**Budget Recommendations:**
```
Current Daily Budget × Traffic Multiplier = Recommended BFCM Budget
Example: $1,000 × 3 = $3,000 recommended
```

**ROI-Based Prioritization:**
```
Historical BFCM ROAS by Campaign
Rank campaigns by ROAS
Allocate more budget to high-ROAS campaigns
```

**Budget Sufficiency Check:**
```
If (Impression Share Lost to Budget > 20%) {
  Flag as "Will definitely run out of budget"
}
```

#### 4. Recommendation Generation

Based on the analysis, the agent creates:

**Executive Summary:**
- Overall readiness score (1-10)
- Total budget needed
- Top 3 priorities
- Key risks

**Budget Table:**
```
| Campaign       | Current Budget | Recommended | Increase | Rationale        |
|----------------|---------------|-------------|----------|------------------|
| Search - Brand | $1,000        | $3,000      | +200%    | 3x traffic surge |
| Display        | $500          | $750        | +50%     | Lower priority   |
```

**Preparation Checklist:**
- Critical tasks (e.g., "Increase Budget on Campaign X by Nov 20")
- Important optimizations (e.g., "Add seasonal keywords")
- Nice-to-have improvements (e.g., "Create countdown ads")

**Monitoring Schedule:**
- When to check performance daily vs. hourly
- Which metrics to watch closely
- Alert thresholds (e.g., "If spend hits 80% of daily budget by 2pm...")

## What You Get

### Outputs

1. **Historical Performance Summary**
   - Previous BFCM results (spend, conversions, ROAS)
   - Year-over-year comparison if multiple years available
   - Peak traffic days identified

2. **Current State Assessment**
   - Campaign health check
   - Budget sufficiency analysis
   - Impression share gaps

3. **Specific Budget Recommendations**
   - Recommended daily budgets by campaign
   - Total account budget needed
   - Timeline for budget increases

4. **Actionable Checklist**
   - Tasks organized by priority (Must/Should/Nice-to-Do)
   - Specific actions with deadlines
   - Campaign-specific recommendations

5. **Monitoring Plan**
   - Pre-event preparation timeline
   - During-event monitoring schedule
   - Post-event analysis framework

### Example Output

```
🎯 Executive Summary
Overall Readiness: 6/10 (Needs Attention)

Top 3 Priorities:
1. Increase budgets on top 3 campaigns by 150% before Nov 20
2. Add Black Friday keywords to 5 underutilized campaigns
3. Ensure conversion tracking working (test before Nov 24)

Total Recommended Budget: $45,000 (currently: $18,000)
Key Risk: Campaign "Search - Brand" will run out of budget by noon on Nov 24

---

📊 Historical Performance (Nov 24-27, 2023)

| Campaign          | 2023 Spend | 2023 Conv | 2023 ROAS | vs. Oct 2023 |
|-------------------|------------|-----------|-----------|--------------|
| Search - Brand    | $8,500     | 142       | 450%      | +280% spend  |
| Search - NonBrand | $6,200     | 98        | 380%      | +310% spend  |
| Shopping          | $4,100     | 67        | 520%      | +250% spend  |

Insight: Traffic surged 3x normal levels. Conversions doubled. ROAS remained strong.

---

💰 Budget Recommendations

| Campaign          | Current | Recommended | Change   | Rationale                    |
|-------------------|---------|-------------|----------|------------------------------|
| Search - Brand    | $5,000  | $15,000     | +200%    | 3x traffic, best ROAS (450%) |
| Search - NonBrand | $4,000  | $12,000     | +200%    | High volume, good ROAS       |
| Shopping          | $3,000  | $9,000      | +200%    | Best ROAS (520%), inventory  |
| Display           | $6,000  | $9,000      | +50%     | Lower priority, awareness    |

Total: $45,000 (up from $18,000)

---

✅ Preparation Checklist

MUST DO (Complete by Nov 20):
- [ ] Increase Search - Brand budget to $15k (currently losing 60% impr share to budget)
- [ ] Test conversion tracking (place test order)
- [ ] Ensure all ads approved (2 currently disapproved)
- [ ] Verify Google Ads billing method has sufficient limit ($50k+)

SHOULD DO (Complete by Nov 22):
- [ ] Add "black friday" keywords to all campaigns (+47 new keywords)
- [ ] Create seasonal ad copy variations (urgency, scarcity messaging)
- [ ] Set up automated budget rules (alert if 80% spent by 2pm)
- [ ] Review negative keywords (avoid wasted spend on "free" etc.)

NICE TO HAVE (If time permits):
- [ ] Create remarketing campaign for cart abandoners
- [ ] Set up countdown ads (creates urgency)
- [ ] Prepare extended holiday campaigns (Dec 1-25)

---

📈 Monitoring Plan

Pre-Event (Nov 15-23):
- Daily: Check budget pacing, impression share
- Nov 20: Implement budget increases
- Nov 22: Final campaign health check

Event Period (Nov 24-27):
- Hourly: Budget pacing (risk of early depletion)
- Every 4 hours: ROAS, conversion rate, impression share
- Morning/Evening: Competitor activity check

Post-Event (Nov 28-30):
- Daily: Wind-down performance
- Nov 30: Full BFCM performance report vs. this forecast

---

⚠️ Risks & Mitigation

RISK: Budget exhaustion by midday Nov 24
Mitigation: Set up automated alert at 50% daily budget spent

RISK: CPCs may spike 100-200%
Mitigation: Use Target ROAS bidding instead of manual CPC

RISK: Conversion tracking delay during high volume
Mitigation: Don't panic - allow 48 hours for full attribution

RISK: Competitor activity increase
Mitigation: Monitor auction insights, be prepared to increase bids
```

## What This Agent Does NOT Do

### No Automatic Changes
- ❌ Does not modify budgets
- ❌ Does not change bids
- ❌ Does not pause/enable campaigns
- ❌ Does not create/edit ads
- ❌ Does not adjust targeting

### No External Access
- ❌ Does not access competitor data (unless you provide it)
- ❌ Does not send data to external APIs
- ❌ Does not store your data anywhere
- ❌ Does not share analysis with anyone

### No Guarantees
- ❌ Does not guarantee results (your results may vary)
- ❌ Does not account for external factors (economy, competitors, etc.)
- ❌ Does not predict exact conversions (uses historical patterns)
- ❌ Does not replace human judgment

## When to Use This Agent

### Ideal Scenarios

**First-time BFCM Preparation:**
- You're new to Black Friday advertising
- Want to understand what to expect
- Need a structured preparation plan

**Annual BFCM Planning:**
- You've run BFCM campaigns before
- Want to compare this year's setup to last year
- Need data-driven budget recommendations

**Budget Justification:**
- Need to show stakeholders why budget increase is needed
- Want historical data to support recommendations
- Building business case for holiday investment

**Account Audit:**
- Reviewing readiness of multiple accounts
- Checking if campaigns are properly configured
- Ensuring nothing critical is missing

### Not Ideal For

**Real-time Optimization:**
- This is for **preparation**, not live management
- Use during the event for monitoring, not pre-planning

**Accounts Without History:**
- Limited value if you have no BFCM history
- Agent will use industry benchmarks but less personalized

**Non-Seasonal Businesses:**
- Designed for retail/e-commerce with BFCM peak
- May not apply to B2B or non-seasonal businesses

## Usage Instructions

### Basic Usage

```
User: "Prepare my account for Black Friday"
```

The agent will:
1. Analyze your historical BFCM data (if available)
2. Assess current campaign setup
3. Calculate budget recommendations
4. Generate preparation checklist
5. Create monitoring plan

### With Budget Constraints

```
User: "Prepare for Black Friday but I only have $10,000 budget"
```

The agent will:
1. Prioritize campaigns by historical ROAS
2. Recommend how to allocate limited budget
3. Suggest which campaigns to pause during peak days
4. Focus budget on highest-return opportunities

### First-Time BFCM

```
User: "This is my first Black Friday, get me ready"
```

The agent will:
1. Acknowledge lack of historical data
2. Use current performance as baseline
3. Apply industry benchmarks (2-3x traffic surge)
4. Provide conservative recommendations
5. Emphasize monitoring and flexibility

### Account-Specific

```
User: "Prepare account 9882330267 for Black Friday"
```

The agent will:
1. Use the specified account ID
2. Run analysis for that account only
3. Provide account-specific recommendations

## Data Privacy & Security

### What Data is Accessed

**From Your Google Ads Account:**
- Campaign names, IDs, status
- Historical spend and performance metrics
- Current budget settings
- Impression share metrics
- Conversion data (totals only, not customer PII)

**NOT Accessed:**
- Customer emails, names, addresses
- Payment information
- Search query details with PII
- User lists or audience data
- Conversion details (only totals)

### Where Data Goes

1. **Your MCP Server** → Queries Google Ads API (read-only)
2. **Claude Code (Local)** → Processes data on your machine
3. **Your Screen** → Displays analysis report
4. **Nowhere Else** → No external storage or transmission

### Data Retention

- ❌ Agent does not store data between sessions
- ❌ No data saved to files (unless you explicitly save the report)
- ❌ No data sent to Anthropic or third parties
- ✅ Data only exists during your Claude Code session

## Limitations & Disclaimers

### What the Agent Knows

**It knows:**
- Your historical Google Ads performance
- Your current campaign setup and budgets
- Industry best practices for BFCM prep
- How to calculate traffic surges and budget needs

**It doesn't know:**
- Your business margins and profitability
- Your inventory levels or stock capacity
- Your website's ability to handle traffic
- Your competitors' plans
- External economic factors
- Your overall marketing mix (email, social, etc.)

### Accuracy Considerations

**Recommendations are based on:**
- Historical patterns (past ≠ guaranteed future)
- Industry benchmarks (your results may vary)
- Current performance trends (which can change)

**Factors that may affect accuracy:**
- Economic conditions (recession, inflation, etc.)
- New competitors entering market
- Platform changes (Google Ads algorithm updates)
- Your product/service changes
- Market demand shifts

### Use Your Judgment

This agent provides **recommendations, not decisions**. Consider:

- Your business context and constraints
- Market conditions and trends
- Your risk tolerance
- Your team's capacity
- Your overall strategy

**Always review and validate recommendations before implementing.**

## Frequently Asked Questions

### Q: Will this agent make changes to my account?
**A:** No. It's read-only. All recommendations require your manual implementation.

### Q: How accurate are the budget recommendations?
**A:** Based on your historical data. If last year showed 3x traffic surge, it recommends budgets for similar surge. Accuracy depends on consistency of market conditions.

### Q: What if I don't have historical BFCM data?
**A:** Agent uses industry benchmarks (typically 2-3x normal traffic). Recommendations will be more conservative and less personalized.

### Q: Can I use this for other seasonal events?
**A:** Yes! While optimized for BFCM, you can adapt it for:
- Christmas/Holiday season
- Back to School
- Valentine's Day
- Any seasonal peak period

Just specify the dates when asking.

### Q: How long does the analysis take?
**A:** Typically 30-60 seconds to pull data and generate recommendations. Depends on account size and date ranges.

### Q: Can I run this multiple times?
**A:** Yes! Run it weekly as you approach BFCM to track preparation progress.

### Q: What if my account has hundreds of campaigns?
**A:** Agent analyzes all campaigns but focuses recommendations on top performers (by spend or ROAS). You can ask it to focus on specific campaign types if needed.

### Q: Is this better than a consultant?
**A:** It complements consultants, not replaces them. Provides data-driven analysis quickly. Consultants add strategic judgment, market knowledge, and implementation support.

### Q: Can I share the output with my team?
**A:** Yes! The report is yours to share. You can copy the output and distribute as needed.

### Q: What about Performance Max campaigns?
**A:** Agent analyzes PMax campaigns same as others. However, detailed asset group analysis is limited by current API capabilities. (Full PMax reporting coming in future API updates.)

## Support & Feedback

### Getting Help

**If the agent isn't working:**
1. Check your MCP server is running
2. Verify you have Google Ads API access
3. Ensure your account has historical data
4. Review error messages for specific issues

**If recommendations seem off:**
1. Verify the date ranges analyzed
2. Check if historical data is complete
3. Consider if your business has changed significantly
4. Apply your own judgment to adjust recommendations

### Provide Feedback

Found this helpful? Have suggestions?
- Open an issue: https://github.com/mikerhodesideas/adstoai-marketplace/issues
- Community discussion: https://8020agent.com

## Version History

**v1.0.0 (October 2024)**
- Initial release
- Historical BFCM analysis
- Budget recommendations
- Preparation checklist
- Monitoring plan

## License

This agent is part of the google-ads-mcp plugin, licensed under Apache 2.0.

---

**Remember: This is a tool to inform your decisions, not make them for you. Use it as one input among many in your BFCM planning process.**
