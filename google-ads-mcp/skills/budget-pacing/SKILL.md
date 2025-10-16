---
name: Calculating Budget Pacing
description: Analyzes budget pacing for Google Ads campaigns, tracking daily and monthly spend against targets. Use when checking if campaigns are on track, over/under-spending, or when forecasting end-of-period budget usage.
allowed-tools: mcp__google-ads__execute_gaql
---

# Calculating Budget Pacing

## Instructions

When the user asks about budget pacing, spending trajectory, or whether campaigns are on track, use this skill to analyze and report on budget performance.

### 1. Understand the Request

Identify what the user needs:
- **Daily pacing**: Is today's spend on track?
- **Monthly pacing**: Will we hit/exceed monthly budget?
- **Campaign-level**: Which campaigns are over/under-pacing?
- **Account-level**: Overall budget health
- **Forecast**: Projected end-of-period spend

### 2. Gather Budget and Spend Data

Execute GAQL queries to get current spend and budget settings:

**For monthly pacing (most common):**
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

**For daily budgets:**
```sql
SELECT
  campaign.name,
  campaign_budget.amount_micros,
  campaign_budget.period,
  segments.date,
  metrics.cost_micros
FROM campaign
WHERE segments.date = TODAY
  AND campaign.status = 'ENABLED'
```

**For campaign budget details:**
```sql
SELECT
  campaign.name,
  campaign.id,
  campaign.status,
  campaign_budget.amount_micros,
  campaign_budget.period,
  campaign_budget.explicitly_shared
FROM campaign
WHERE campaign.status = 'ENABLED'
```

### 3. Calculate Pacing Metrics

For each campaign with monthly budgets:

**Key Calculations:**

1. **Days Elapsed** = Current day of month (e.g., Oct 17 = day 17)
2. **Days in Month** = Total days in current month (e.g., October = 31)
3. **Expected Spend** = (Budget / Days in Month) × Days Elapsed
4. **Actual Spend** = Sum of cost_micros for month to date
5. **Pacing %** = (Actual Spend / Expected Spend) × 100
6. **Remaining Budget** = Budget - Actual Spend
7. **Daily Run Rate** = Actual Spend / Days Elapsed
8. **Projected End-of-Month Spend** = Daily Run Rate × Days in Month
9. **Variance** = Projected Spend - Budget

**Pacing Status:**
- **On Track**: Pacing between 95-105%
- **Under-pacing**: Pacing < 95%
- **Over-pacing**: Pacing > 105%
- **At Risk**: Projected to exceed budget by >10%

### 4. Present Results

Format output in clear tables:

#### Campaign Budget Pacing (Month-to-Date)

| Campaign | Budget | Spent | Expected | Pacing | Status | Projected EOM | Variance |
|----------|--------|-------|----------|--------|--------|---------------|----------|
| Campaign A | $5,000 | $2,800 | $2,750 | 102% | On Track | $5,100 | +$100 |
| Campaign B | $3,000 | $1,200 | $1,650 | 73% | Under | $2,200 | -$800 |
| Campaign C | $2,000 | $1,500 | $1,100 | 136% | OVER | $2,700 | +$700 ⚠️ |

#### Summary Insights

Provide actionable insights:
- **Total Account Budget**: $10,000
- **Total Spent**: $5,500 (55% of total)
- **Days Elapsed**: 17 of 31 (55% of month)
- **Overall Pacing**: 100% ✅
- **Projected Total**: $10,000

**Alerts:**
- ⚠️ Campaign C is over-pacing by 36% and will exceed budget by $700
- 💡 Campaign B is under-pacing - could increase bids or budget
- ✅ Campaign A is pacing perfectly

### 5. Provide Recommendations

Based on pacing status, suggest actions:

**For Over-Pacing Campaigns:**
- Reduce daily budget to slow spending
- Lower bids to reduce cost per click
- Tighten targeting to reduce impression volume
- Calculate recommended daily budget: `Remaining Budget / Days Left`

**For Under-Pacing Campaigns:**
- Increase daily budget if performance is good
- Raise bids to increase competitiveness
- Expand targeting to capture more volume
- Check for impression share lost to budget

**For On-Track Campaigns:**
- Monitor daily
- No immediate action needed
- Watch for weekend/seasonal fluctuations

### 6. Handle Edge Cases

**Shared Budgets:**
- Group campaigns by shared budget
- Calculate pacing at budget level, not campaign level
- Show all campaigns using the shared budget

**Daily Budgets (not monthly):**
- Compare actual spend today vs. budget
- Show 7-day average vs. budget
- Project 30-day spend based on current pace

**New Campaigns (< 7 days):**
- Note insufficient data for accurate pacing
- Use available days for projection
- Flag as "New - Monitor Closely"

**Paused/Disabled Campaigns:**
- Exclude from calculations
- Note if recently paused (may affect budget allocation)

## Output Format

Always structure your response with:

1. **Quick Summary** (2-3 sentences)
   - Overall pacing status
   - Key alerts
   - Recommended actions

2. **Detailed Table** (all active campaigns)
   - Budget, spend, pacing percentage
   - Status indicators (✅ ⚠️ 🔴)

3. **Insights** (bullet points)
   - Campaigns needing attention
   - Forecast vs. targets
   - Optimization opportunities

4. **Recommendations** (specific actions)
   - What to do about over/under-pacing
   - Budget adjustment calculations
   - Monitoring schedule

## Data Formatting

- **Costs**: Always convert micros to currency (÷ 1,000,000)
- **Percentages**: Round to whole numbers (102%, not 102.34%)
- **Currency**: Use user's account currency (check from account data)
- **Dates**: Use clear date formats (Oct 17, not 2024-10-17)
- **Status**: Use visual indicators (✅ ⚠️ 🔴) when possible

## Common User Questions

**"How is my budget pacing?"**
→ Show month-to-date pacing for all campaigns

**"Will I hit my monthly budget?"**
→ Show projected end-of-month spend vs. budget

**"Which campaigns are overspending?"**
→ Filter and highlight campaigns pacing > 110%

**"Am I under-spending?"**
→ Show campaigns pacing < 90% with recommendations

**"What should my daily budget be?"**
→ Calculate: (Total Budget - Spent) / Days Remaining

**"How much budget is left?"**
→ Show remaining budget and days left

## Key Formulas Reference

```
Expected Daily Spend = Monthly Budget / Days in Month

Expected Spend to Date = Expected Daily Spend × Days Elapsed

Pacing Percentage = (Actual Spend / Expected Spend) × 100

Daily Run Rate = Actual Spend / Days Elapsed

Projected EOM Spend = Daily Run Rate × Days in Month

Days Remaining = Days in Month - Days Elapsed

Recommended Daily Budget = Remaining Budget / Days Remaining
```

## Example Interaction

**User:** "How is my budget pacing this month?"

**You respond:**

**📊 Budget Pacing Summary (October 17/31)**

Overall: Your account is slightly over-pacing at 108%. You've spent $5,500 of $10,000 budget with 14 days remaining.

| Campaign | Budget | Spent | Pacing | Status | Projected |
|----------|--------|-------|--------|--------|-----------|
| Search - Brand | $5,000 | $2,800 | 102% | ✅ On Track | $5,100 |
| Search - Non-Brand | $3,000 | $1,200 | 73% | ⚠️ Under | $2,200 |
| Display | $2,000 | $1,500 | 136% | 🔴 Over | $2,700 |

**Key Insights:**
- Display campaign will exceed budget by ~$700 if current pace continues
- Non-Brand search is under-spending by $800
- Brand search is pacing perfectly

**Recommended Actions:**
1. **Display Campaign** - Reduce daily budget to $36 (currently $64) to pace correctly
2. **Non-Brand Campaign** - Consider increasing budget or bids if performance is good
3. **Overall** - Reallocate $500 from Non-Brand to Display if display ROAS is good

---

**Remember:** Budget pacing is about trajectory, not absolutes. A campaign at 110% pacing with 20 days left is fine. A campaign at 110% pacing with 2 days left is critical.
