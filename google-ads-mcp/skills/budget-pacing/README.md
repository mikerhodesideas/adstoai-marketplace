# Budget Pacing Skill

## What This Skill Does

The Budget Pacing Skill is a **read-only analysis tool** that tracks your Google Ads campaign spending against budget targets. It tells you if you're on track, over-spending, or under-spending, and provides specific recommendations for budget adjustments.

**Important: This skill NEVER changes your budgets.** It only reads your spend data and provides analysis. You remain in full control of all budget decisions.

## Why You Can Trust This Skill

### Read-Only Access
- **No budget changes**: Cannot modify campaign budgets
- **No bid adjustments**: Cannot change bidding strategies
- **No campaign edits**: Cannot pause, enable, or modify settings
- **Analysis only**: Reads spend data, calculates pacing, displays results

### Transparency
- All calculations shown in output
- GAQL queries documented below
- Formula explanations provided
- No hidden logic or black boxes

### Privacy
- Data stays in your Claude Code session
- No external API calls beyond your MCP server
- No data storage between sessions
- No third-party data sharing

## How It Works

### What is "Budget Pacing"?

Budget pacing tells you if your spending is on track to:
- Hit your monthly budget target (not waste budget)
- Stay within budget (not overspend)
- Distribute spend evenly across the month (not run out early)

**Example:**
- You have a $10,000 monthly budget
- Today is October 15 (halfway through the month)
- You've spent $5,000
- **Pacing = 100%** ✅ On track!

If you've spent $6,000 (60% of budget) but only 50% of month passed:
- **Pacing = 120%** ⚠️ Over-pacing (may exceed budget)

If you've spent $4,000 (40% of budget) when 50% of month passed:
- **Pacing = 80%** 💡 Under-pacing (may under-utilize budget)

### Step-by-Step Process

#### 1. Gather Spend Data

The skill queries your Google Ads account for current month spend:

**GAQL Query:**
```sql
SELECT
  campaign.name,
  campaign.id,
  campaign_budget.amount_micros,
  campaign_budget.period,
  segments.date,
  metrics.cost_micros
FROM campaign
WHERE segments.date DURING THIS_MONTH
  AND campaign.status = 'ENABLED'
ORDER BY campaign.name, segments.date
```

**What This Gets:**
- Campaign name and ID (for identification)
- Budget amount (your target)
- Budget period (daily vs. monthly)
- Each day's spend (to calculate total)
- Only active campaigns (ignores paused)

**Why These Fields?**
- `campaign.name` - To show you which campaign
- `campaign_budget.amount_micros` - Your monthly budget target
- `segments.date` - To sum up spend for month-to-date
- `metrics.cost_micros` - Actual spend (in micros, divided by 1M for currency)

#### 2. Calculate Pacing Metrics

For each campaign, the skill calculates:

**A. Days Elapsed**
```
Today's Date - First Day of Month + 1
Example: Oct 15 - Oct 1 + 1 = 15 days elapsed
```

**B. Total Days in Month**
```
Last Day of Current Month
Example: October has 31 days
```

**C. Expected Spend (what you SHOULD have spent)**
```
Expected Spend = (Monthly Budget ÷ Total Days) × Days Elapsed

Example:
Monthly Budget = $10,000
Days in Month = 31
Days Elapsed = 15
Expected Spend = ($10,000 ÷ 31) × 15 = $4,838.71
```

**D. Actual Spend (what you HAVE spent)**
```
Sum all cost_micros from start of month to today, divided by 1,000,000

Example:
Oct 1-15 total cost_micros = 5,000,000,000
Actual Spend = 5,000,000,000 ÷ 1,000,000 = $5,000
```

**E. Pacing Percentage**
```
Pacing % = (Actual Spend ÷ Expected Spend) × 100

Example:
Actual = $5,000
Expected = $4,838.71
Pacing = ($5,000 ÷ $4,838.71) × 100 = 103%
```

**F. Daily Run Rate**
```
Daily Run Rate = Actual Spend ÷ Days Elapsed

Example:
Actual Spend = $5,000
Days Elapsed = 15
Daily Run Rate = $5,000 ÷ 15 = $333.33/day
```

**G. Projected End-of-Month Spend**
```
Projected Spend = Daily Run Rate × Total Days in Month

Example:
Daily Run Rate = $333.33
Days in Month = 31
Projected = $333.33 × 31 = $10,333.33
```

**H. Variance from Budget**
```
Variance = Projected Spend - Monthly Budget

Example:
Projected = $10,333.33
Budget = $10,000
Variance = +$333.33 (will exceed by $333)
```

#### 3. Determine Status

Based on pacing percentage:

| Pacing % | Status | Meaning | Icon |
|----------|--------|---------|------|
| 95-105% | On Track | Spending as expected | ✅ |
| 90-95% | Slightly Under | Might under-spend | 💡 |
| < 90% | Under-Pacing | Significantly under budget | ⚠️ |
| 105-110% | Slightly Over | Might slightly exceed | ⚠️ |
| > 110% | Over-Pacing | Will likely exceed budget | 🔴 |

#### 4. Generate Recommendations

For each pacing status, specific actions are recommended:

**Over-Pacing (>110%):**
- Calculate recommended daily budget to stay on track
- Suggest bid reductions to lower cost
- Identify if impression share is already maxed out
- Show exactly how much to reduce budget

**Under-Pacing (<90%):**
- Calculate recommended budget increase
- Check if impression share is being lost to budget
- Suggest raising bids if performance is good
- Show opportunity cost of under-spending

**On Track (95-105%):**
- No changes needed
- Monitor daily for sudden changes
- Watch for weekend/weekday patterns

### 5. Format and Display Results

Output is presented in tables with clear indicators:

**Summary Table:**
```
Campaign Budget Pacing (October 15/31)

| Campaign      | Budget  | Spent   | Expected | Pacing | Status   | Projected | Variance |
|---------------|---------|---------|----------|--------|----------|-----------|----------|
| Brand Search  | $10,000 | $5,000  | $4,839   | 103%   | ✅ Track | $10,333   | +$333    |
| Display       | $5,000  | $2,000  | $2,419   | 83%    | ⚠️ Under | $4,133    | -$867    |
| Shopping      | $8,000  | $6,000  | $3,871   | 155%   | 🔴 Over  | $12,400   | +$4,400  |
```

**Actionable Insights:**
- Total account pacing
- Campaigns needing attention
- Specific budget adjustment calculations
- Monitoring recommendations

## What You Get

### Real-Time Pacing Status

**For each campaign:**
- Current spend vs. target
- Pacing percentage (ahead/behind/on track)
- Projected end-of-month spend
- Budget variance (over/under by how much)

**For your account:**
- Overall pacing across all campaigns
- Total budget utilization
- Aggregate projections

### Specific Recommendations

**Budget Adjustment Calculations:**
```
Current Situation:
- Budget: $8,000
- Spent: $6,000 (15 days)
- Projected: $12,400
- Overage: +$4,400

Recommendation:
- Reduce daily budget to: $129 (currently $258)
- Calculation: ($8,000 - $6,000) ÷ 16 days remaining = $125/day
- This keeps you on track to hit $8,000 exactly
```

**Performance Context:**
```
Campaign is over-pacing at 155%

Possible reasons:
1. High auction activity (competitors bidding up)
2. Budget too high for traffic available
3. Bid strategy too aggressive

Actions:
1. Reduce daily budget from $258 to $129
2. Or: Switch to Target CPA/ROAS to control costs
3. Or: Accept overage if ROAS is good
```

### Forecasting

**Spend Projections:**
- What you'll spend by month-end at current pace
- How much budget remains
- How many days of budget left
- When you'll run out (if over-pacing)

**Scenario Analysis:**
- "If I reduce budget to $X, what's my new pacing?"
- "If I keep current pace, when do I exhaust budget?"
- "What budget do I need to spend all of it evenly?"

## What This Skill Does NOT Do

### No Automatic Changes
- ❌ Does not modify budgets
- ❌ Does not change bids
- ❌ Does not pause campaigns
- ❌ Does not adjust targeting
- ❌ Does not create alerts or rules

### No Predictions
- ❌ Does not predict conversion rates
- ❌ Does not forecast sales/revenue
- ❌ Does not account for seasonality
- ❌ Does not consider external events

### No Strategy
- ❌ Does not tell you if over-spending is good/bad
- ❌ Does not optimize for ROAS
- ❌ Does not determine if performance justifies spend
- ❌ Does not compare to industry benchmarks

**Your judgment required:** Pacing is one metric. Performance (ROAS, CPA) matters more. Over-pacing might be fine if returns are great!

## When to Use This Skill

### Daily Monitoring

**Best for:**
- Quick morning check: "Am I on track today?"
- Weekly review: "How's pacing this week?"
- Mid-month assessment: "Will I hit my budget?"

**Usage:**
```
"How is my budget pacing?"
"Am I on track with spending?"
"Check budget pacing for all campaigns"
```

### Budget Planning

**Before month-end:**
- Forecast final spend
- Plan next month's budgets
- Identify consistently over/under-pacing campaigns

**Usage:**
```
"Will I hit my monthly budget?"
"What will I spend by end of month?"
"Which campaigns are off pace?"
```

### Issue Detection

**Alert scenarios:**
- Budget exhaustion risk (pacing >150%)
- Under-utilization (pacing <50%)
- Sudden pacing changes (was 100%, now 200%)

**Usage:**
```
"Which campaigns are overspending?"
"Am I running out of budget?"
"Show me campaigns at risk"
```

### Budget Optimization

**Finding opportunities:**
- Under-spending campaigns (can allocate more)
- Over-spending low-performers (can reduce)
- Budget reallocation opportunities

**Usage:**
```
"Where can I reallocate budget?"
"Which campaigns aren't using their budget?"
"What's my budget utilization?"
```

## Common Scenarios & Examples

### Scenario 1: Mid-Month Check

**User asks:** "How is my budget pacing this month?"

**Skill provides:**

```
📊 Budget Pacing Summary (October 15/31)

Overall: Your account is pacing at 103% - slightly over but within acceptable range.
You've spent $23,500 of $45,000 total budget.

Campaign Details:

| Campaign         | Budget  | Spent   | Pacing | Status    | Action Needed?      |
|------------------|---------|---------|--------|-----------|---------------------|
| Search - Brand   | $10,000 | $5,000  | 100%   | ✅ Track  | None - perfect pace |
| Search - Generic | $15,000 | $8,200  | 109%   | ⚠️ Over  | Monitor daily       |
| Shopping         | $12,000 | $7,800  | 130%   | 🔴 Over  | Reduce to $250/day  |
| Display          | $8,000  | $2,500  | 63%    | ⚠️ Under | Increase if ROI OK  |

Key Insights:
- Shopping campaign will exceed budget by ~$3,600 at current pace
- Display is under-spending by ~$2,600
- Could reallocate from Display to Shopping if Shopping performance is good
- Overall account will hit ~$47,400 (2% over total budget)

Recommendations:
1. Shopping: Reduce daily budget to $250 (currently $388)
2. Display: Check impression share - if high, budget is sufficient
3. Generic Search: Monitor but no action yet (9% over is acceptable)
```

### Scenario 2: Budget Crisis

**User asks:** "Am I running out of budget?"

**Skill provides:**

```
⚠️ BUDGET ALERT: Critical Over-Pacing Detected

Campaign: Shopping - Holiday Gifts
Current Status: 247% pacing 🔴

Details:
- Monthly Budget: $5,000
- Spent to Date: $4,200 (Day 10 of 31)
- Expected Spend: $1,700
- Daily Run Rate: $420/day

Projection:
- At current pace: $13,020 total (will exceed budget by day 12)
- Budget will be exhausted: October 12 at ~5pm
- Days remaining without budget: 19 days

IMMEDIATE ACTION REQUIRED:
1. Reduce daily budget to $38 (currently $161)
   Calculation: ($5,000 - $4,200) ÷ 21 days left = $38/day
2. Or: Pause campaign until you can increase monthly budget
3. Or: Accept overage and request budget increase

Why This Happened:
- Traffic surge (possible seasonal spike or competitor change)
- Bidding strategy may be too aggressive
- Check auction insights for unusual activity
```

### Scenario 3: Under-Spending

**User asks:** "Why am I not spending my budget?"

**Skill provides:**

```
💡 Under-Pacing Analysis

Campaign: Display - Remarketing
Current Status: 45% pacing

Details:
- Monthly Budget: $8,000
- Spent to Date: $1,800 (Day 15 of 31)
- Expected Spend: $4,000
- Daily Average: $120/day

Projection:
- At current pace: $3,720 total
- Under-budget by: $4,280 (54% unused)

Why You're Under-Spending:
[Skill queries for additional context:]

Impression Share Data:
- Current Impression Share: 78%
- Lost to Budget: 2%
- Lost to Rank: 20%

Analysis:
- Budget is NOT the limiting factor (only 2% lost to budget)
- Low impression share due to ad rank (bids too low or quality score issues)
- Even with current budget, you're not capturing available traffic

Recommendations:
1. INCREASE BIDS (not budget) - you're losing traffic to rank, not budget
2. Improve ad quality score (better ads, landing pages)
3. Consider reducing budget to $4,000 (matches actual utilization)
4. Or: Accept under-spend and reallocate to campaigns hitting limits
```

### Scenario 4: Planning Next Month

**User asks:** "What should my budget be next month?"

**Skill provides:**

```
📈 Budget Planning Recommendations (for November)

Based on October Performance:

| Campaign         | Oct Budget | Oct Actual | Pacing | Rec. Nov Budget | Change   | Rationale                          |
|------------------|------------|------------|--------|-----------------|----------|-------------------------------------|
| Search - Brand   | $10,000    | $10,200    | 102%   | $10,500         | +5%      | Slight overage, growing demand      |
| Shopping         | $12,000    | $15,600    | 130%   | $16,000         | +33%     | Consistent overspend, strong ROAS   |
| Display          | $8,000     | $5,000     | 63%    | $5,500          | -31%     | Align budget with actual usage      |
| Generic Search   | $15,000    | $14,100    | 94%    | $15,000         | No change| Well-paced, keep steady             |

Total Recommended: $47,000 (was $45,000)

Notes:
- Shopping shows strong demand - increase budget
- Display not utilizing budget - reduce to actual need
- Overall increase of $2,000 (+4.4%) reflects growth
- Consider seasonality: November into Black Friday may need further increases
```

## Data Privacy & Security

### What Data is Accessed

**From Your Google Ads Account:**
- Campaign names and IDs
- Budget settings (amount, period)
- Daily spend amounts (cost_micros)
- Campaign status (enabled/paused)

**NOT Accessed:**
- Customer information (emails, names)
- Click/impression details
- Keyword-level data
- Audience lists
- Conversion details beyond totals

### Where Data Goes

1. **Your MCP Server** → Queries Google Ads API
2. **Claude Code (Local)** → Calculates pacing on your machine
3. **Your Screen** → Displays results
4. **Nowhere Else** → No storage, no transmission

### Data Retention

- ❌ No data stored between sessions
- ❌ No cloud storage or external databases
- ❌ No data sent to Anthropic servers
- ✅ Data only in active Claude Code session
- ✅ Disappears when you close Claude Code

## Accuracy & Limitations

### What Affects Accuracy

**Timing:**
- Skill uses data as of query time
- Spend may update hourly (not real-time)
- Some conversions have attribution delay

**Budget Types:**
- Works best with monthly budgets
- Daily budgets show 7-day average pacing
- Shared budgets grouped together

**Edge Cases:**
- New campaigns (< 7 days) flagged as "insufficient data"
- Recently paused campaigns may affect calculations
- Budget changes mid-month can skew pacing

### Known Limitations

**Does not account for:**
- Weekday vs. weekend patterns (assumes even daily spend)
- Seasonal surges (unless they've already started)
- Upcoming campaign changes you plan to make
- Other marketing channels affecting demand

**Calculation assumptions:**
- Even daily spend distribution (but spend often varies)
- Current pace continues (but pace can change)
- No external shocks (market events, competitors)

### Use Your Judgment

Pacing is a **guide, not a rule**.

**Over-pacing might be fine if:**
- ROAS is high (returns justify spending)
- Seasonal opportunity (capture traffic while available)
- Competitor weakness (take market share now)

**Under-pacing might be fine if:**
- Impression share is high (budget sufficient for traffic)
- ROAS is weak (don't force spend on poor performance)
- Saving budget for later in month (planned strategy)

**Always consider performance alongside pacing.**

## Frequently Asked Questions

### Q: Is 100% pacing required?
**A:** No! 95-105% is "on track." Some variance is normal. Focus on performance (ROAS/CPA) more than perfect pacing.

### Q: What if I want to over-pace?
**A:** That's fine! Over-pacing is only a problem if it exceeds available budget or hurts performance. High ROAS justifies over-pacing.

### Q: How often should I check pacing?
**A:**
- Daily monitoring: Quick checks for early alerts
- Weekly reviews: Detailed analysis and adjustments
- Mid-month: Forecast and reallocation decisions

### Q: Can I check pacing for a specific date range?
**A:** Yes! Ask like: "What was my pacing for the first week of October?"

### Q: What about shared budgets?
**A:** Skill groups campaigns by shared budget and calculates pacing at budget level (not individual campaign).

### Q: Does this work with daily budgets?
**A:** Yes, but shows 7-day average pacing instead of monthly. Less useful for daily budgets since they reset daily.

### Q: What if my campaigns have different goals?
**A:** Pacing is budget-neutral. It doesn't judge if spending is good/bad, just if you're on track. Apply your own performance criteria.

### Q: Can I export pacing reports?
**A:** You can copy the output and paste into spreadsheets, emails, or reports.

### Q: What about Performance Max campaigns?
**A:** Works the same as any campaign type. Analyzes spend vs. budget regardless of campaign structure.

### Q: How is this different from Google Ads UI?
**A:** Google Ads shows current spend. This skill:
- Calculates expected spend
- Computes pacing percentage
- Projects end-of-month
- Recommends specific budget adjustments

It's analysis on top of the raw data.

## Formulas Reference

All calculations are transparent and documented:

```
Days Elapsed = Current Day of Month

Expected Spend = (Monthly Budget ÷ Days in Month) × Days Elapsed

Pacing % = (Actual Spend ÷ Expected Spend) × 100

Daily Run Rate = Actual Spend ÷ Days Elapsed

Projected End-of-Month = Daily Run Rate × Days in Month

Variance = Projected Spend - Monthly Budget

Recommended Daily Budget = Remaining Budget ÷ Days Remaining
```

You can verify calculations using your own data and a calculator.

## Support & Feedback

### Getting Help

**Skill not working?**
1. Verify MCP server is running
2. Check account has budget data
3. Ensure campaigns are enabled
4. Review error messages

**Results seem incorrect?**
1. Verify date range being analyzed
2. Check for mid-month budget changes
3. Confirm spend data is complete
4. Consider weekend/weekday variance

### Provide Feedback

Suggestions or issues:
- GitHub: https://github.com/mikerhodesideas/adstoai-marketplace/issues
- Community: https://8020agent.com

## Version History

**v1.0.0 (October 2024)**
- Initial release
- Monthly pacing calculations
- Over/under-pacing detection
- Budget recommendations
- Projection forecasting

## License

Part of google-ads-mcp plugin, Apache 2.0 license.

---

**Remember: Pacing is a tool for budget management, not a performance metric. Always prioritize ROAS/CPA over perfect pacing.**
