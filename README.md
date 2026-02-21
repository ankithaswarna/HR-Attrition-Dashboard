# HR Employee Attrition Analytics Dashboard

A comprehensive Power BI dashboard analyzing employee attrition patterns, identifying retention risks, and providing data-driven recommendations with ROI calculations for a 1,470-employee organization.

![Dashboard Preview]![HR Dashboard](Images/HR%20Attrition%20Dashboard.png)

## 📊 Project Overview

This end-to-end HR analytics project identifies why employees leave and provides actionable retention strategies backed by financial analysis. The dashboard revealed a **$13.9M annual attrition cost** and presents targeted interventions with **3-40× ROI potential**.

### Key Business Impact
- **Identified**: 147 high-risk employees likely to leave
- **Discovered**: Employees who left earned 30% less ($4.8K vs $6.8K monthly)
- **Recommended**: 3 interventions projected to reduce attrition from 16.1% to 11.3%
- **Potential Savings**: $3.5M through strategic retention initiatives

---

## 🎯 Business Problem

**Challenge**: Organization experiencing 16.1% annual attrition (237 employees), significantly above industry benchmark of 12-13%.

**Questions Answered**:
1. Why are employees leaving?
2. Which employees are at highest risk?
3. What interventions will have the greatest impact?
4. What is the ROI of retention strategies?

---

## 📁 Dataset

- **Source**: IBM HR Analytics Employee Attrition Dataset
- **Records**: 1,470 employees
- **Features**: 35 attributes including demographics, compensation, satisfaction, performance, tenure
- **Target**: Attrition (Yes/No) - 237 departures, 1,233 active

### Key Attributes
- Employee demographics (Age, Gender, Education, Marital Status)
- Compensation (Monthly Income, Stock Options, Salary Hikes)
- Job details (Department, Role, Job Level, Years at Company)
- Satisfaction metrics (Job, Environment, Work-Life Balance, Relationship)
- Performance (Rating, Promotions, Training)

---

## 🛠️ Tools & Technologies

- **Power BI Desktop** - Dashboard development & visualization
- **DAX (Data Analysis Expressions)** - Advanced calculations & measures
- **Power Query** - Data transformation & modeling
- **Microsoft Excel** - Initial data exploration

---

## 📈 Dashboard Pages

### Page 1: Executive Overview
High-level KPIs and attrition breakdown

**Key Metrics**:
- Total Employees: 1,470
- Attrition Rate: 16.1% (237 employees)
- Active Employees: 1,233
- Average Income: $6,503/month

**Visualizations**:
- Attrition by Department (Donut Chart) - Sales 38%, R&D 53%, HR 12%
- Attrition by Age Group (Stacked Bar)
- Attrition by Job Role (Horizontal Bar)
- Attrition by Tenure (Line Chart) - Spike in years 0-2

**Key Insight**: First-year employees account for 35% of all attrition

---

### Page 2: Demographic Analysis
Deep dive into employee characteristics and patterns

**Interactive Filters**:
- Gender (Male/Female)
- Department (Sales/R&D/HR)
- Education Level (1-5 scale)

**Visualizations**:
- Gender breakdown with attrition split (Stacked Bar)
- Salary vs Tenure correlation (Scatter Plot)
- Education Field attrition rates (Treemap)
- Marital Status impact (Clustered Column)

**Key Insight**: Single employees have 2× higher attrition than married (25.5% vs 12.5%)

---

### Page 3: Compensation Analysis
Salary analysis revealing the pay gap between leavers and stayers

**Key Metrics**:
- Active Employees Avg Salary: $6,832/month
- Departed Employees Avg Salary: $4,787/month
- **Salary Gap: $2,045 (30% difference)**

**Visualizations**:
- Salary comparison KPI cards
- Salary range by department (Min/Avg/Max)
- Employees by salary band with attrition split
- Income progression by job level

**Key Insight**: Low salary is the #1 predictor of attrition

---

### Page 4: Retention Strategy
Data-driven recommendations with ROI calculations

**Financial Analysis**:
- Current Attrition Cost: $13.87M
- Projected Cost (2026): $15.95M (+15% if no action)
- Potential Savings: $1.28M
- Target Attrition Rate: 11% (30% reduction)

**Top 3 Recommendations**:

1. **Salary Correction** (ROI: 3×)
   - Target: 47 low-earning Sales employees
   - Cost: $282K annual raises
   - Expected retention: 19 people
   
2. **Flexible Work Policy** (ROI: 42×)
   - Target: 289 overtime employees
   - Cost: $30K setup
   - Expected retention: 58 people
   
3. **Promotion Review** (ROI: 2.2×)
   - Target: 27 employees with 3+ years no promotion
   - Cost: $210K
   - Expected retention: 8 people

**Visualizations**:
- Priority Matrix (Treemap) - 8 interventions by cost
- Action Cards with ROI calculations
- Risk Heatmap (Job Role × Tenure)

---

## 🧮 Technical Implementation

### DAX Measures Created

```dax
// Basic Metrics
TotalEmployees = COUNTROWS('HR-Employee-Attrition')

TotalAttrition = 
CALCULATE(
    COUNTROWS('HR-Employee-Attrition'),
    'HR-Employee-Attrition'[Attrition] = "Yes"
)

AttritionRate = DIVIDE([TotalAttrition], [TotalEmployees], 0) * 100

// Salary Analysis
AvgSalary_Active = 
CALCULATE(
    AVERAGE('HR-Employee-Attrition'[MonthlyIncome]),
    'HR-Employee-Attrition'[Attrition] = "No"
)

AvgSalary_Attrition = 
CALCULATE(
    AVERAGE('HR-Employee-Attrition'[MonthlyIncome]),
    'HR-Employee-Attrition'[Attrition] = "Yes"
)

// Financial Impact
AvgReplacementCost = 
VAR AvgAnnualSalary = [AvgMonthlyIncome] * 12
VAR ReplacementPercent = 0.75
RETURN AvgAnnualSalary * ReplacementPercent

AttritionCost_Current = [TotalAttrition] * [AvgReplacementCost]

// Risk Identification
HighRiskCount = 
CALCULATE(
    COUNTROWS('HR-Employee-Attrition'),
    'HR-Employee-Attrition'[FlightRiskScore] >= 4,
    'HR-Employee-Attrition'[Attrition] = "No"
)

PotentialSavings = 
VAR HighRisk = [HighRiskCount]
VAR SuccessRate = 0.40
RETURN HighRisk * [AvgReplacementCost] * SuccessRate
```

### Calculated Columns

```dax
// Age Groups
AgeGroup = 
SWITCH(TRUE(),
    [Age] < 25, "Under 25",
    [Age] < 35, "25-34",
    [Age] < 45, "35-44",
    [Age] < 55, "45-54",
    "55+"
)

// Salary Bands
SalaryBand = 
SWITCH(TRUE(),
    [MonthlyIncome] < 3000, "Low (<$3K)",
    [MonthlyIncome] < 6000, "Medium ($3-6K)",
    [MonthlyIncome] < 10000, "High ($6-10K)",
    "Very High (>$10K)"
)

// Flight Risk Score
FlightRiskScore = 
VAR LowSalary = IF([MonthlyIncome] < 3000, 2, 0)
VAR LowSatisfaction = IF([JobSatisfaction] <= 2, 2, 0)
VAR HighTenure = IF([YearsAtCompany] >= 5, -1, 0)
VAR Overtime = IF([OverTime] = "Yes", 1, 0)
VAR RecentPromotion = IF([YearsSinceLastPromotion] > 3, 1, 0)
RETURN LowSalary + LowSatisfaction + HighTenure + Overtime + RecentPromotion
```

---

## 💡 Key Findings

### Demographics
- **Gender**: Males have slightly higher attrition (17% vs 14.8%)
- **Age**: Highest attrition in 25-34 age group
- **Marital Status**: Single employees 2× more likely to leave
- **Tenure**: 35% of attrition occurs in first 2 years

### Compensation
- **Salary Gap**: Leavers earned 30% less than stayers
- **Low Earners**: 47 Sales employees under $3K/month at high risk
- **Job Level**: Entry-level (Levels 1-2) have highest attrition

### Departmental
- **Sales**: Highest attrition rate (21%)
- **R&D**: Highest volume (133 departures)
- **HR**: Smallest but 26% rate

### Satisfaction
- **Low Job Satisfaction** (≤2): 40% attrition rate
- **Overtime Workers**: 3× higher attrition
- **No Promotion 3+ years**: 2.5× higher attrition

---

## 📊 Skills Demonstrated

### Technical Skills
- **DAX**: 15+ measures including CALCULATE, DIVIDE, SWITCH, IF, time intelligence
- **Data Modeling**: Calculated columns, relationships, star schema principles
- **Power Query**: Data transformation, type conversion, column creation
- **Visualization Design**: 20+ charts across 4 coordinated pages
- **Business Intelligence**: KPIs, comparative analysis, trend identification

### Analytical Skills
- Root cause analysis (identifying salary gap as primary driver)
- Predictive modeling (FlightRiskScore algorithm)
- Financial analysis (ROI calculations, cost-benefit analysis)
- Segmentation strategy (identifying high-impact employee groups)
- Strategic recommendations (prioritized action plan)

### Business Skills
- Stakeholder communication (executive-friendly dashboard design)
- Problem-solving (translating data into actionable recommendations)
- Strategic thinking (30% reduction goal based on intervention capacity)
- ROI quantification ($3.5M savings projection)

---

## 🚀 How to Use This Dashboard

### Prerequisites
- Power BI Desktop (Free version works)
- Dataset: [`IBM_HR_Employee_Attrition.csv`](https://www.kaggle.com/code/faressayah/ibm-hr-analytics-employee-attrition-performance) (included in `/data` folder)

### Installation Steps
1. Download this repository
2. Open `HR_Attrition_Dashboard.pbix` in Power BI Desktop
3. If data source prompts appear, navigate to the `/data` folder
4. Click "Refresh" to load data
5. Explore the 4 dashboard pages

### Navigation
- Use tabs at bottom to switch between pages
- Apply slicers (filters) to drill into specific segments
- Hover over visuals for detailed tooltips
- Click visuals to cross-filter the entire page

---

## 📸 Screenshots

### Executive Dashboardpng
![Page 1](Images/HR%20Attrition%20Dashboard.png)

### Demographics
![Page 2]Images/HR%20Demographic%20Analysis.png

### Compensation
![Page 3]Images/Compensation%20Analysis.png
### Strategy
![Page 4]Images/Retention%20Strategy.png

---

## 🎓 Learning Outcomes

Through this project, I developed expertise in:

1. **Business Problem Definition**: Translating organizational challenges into analytical questions
2. **Data Exploration**: Identifying patterns in 35-dimensional employee data
3. **Advanced DAX**: Creating complex calculated measures and columns
4. **Dashboard Design**: Building executive-ready visualizations with clear storytelling
5. **Financial Analysis**: Quantifying business impact in dollar terms
6. **Strategic Thinking**: Moving from insights to prioritized recommendations

---

## 📝 Future Enhancements

- [ ] Time-series analysis with monthly snapshots
- [ ] Predictive modeling using Python (scikit-learn)
- [ ] Exit interview text analysis (NLP)
- [ ] Manager performance correlation
- [ ] Automated alerting for high-risk employees
- [ ] What-if scenario planning (salary adjustment simulator)

---

## 📧 Contact

**Ankitha Swarna**  
Data Analyst | Fresher  
📍 Visakhapatnam, Andhra Pradesh (Open to relocate)  
📧 ankithaswarna123@gmail.com  
📱 +91 9550143733  
🔗 [www.linkedin.com/in/ankithaswarna-data



---

## 📄 License

This project uses the IBM HR Analytics Employee Attrition dataset, which is publicly available on Kaggle for educational and analytical purposes.

---

## 🙏 Acknowledgments

- IBM for providing the HR Analytics dataset
- Power BI community for DAX tutorials and best practices
- SHRM for industry benchmarks on replacement costs

---

*Built with Power BI Desktop | Last Updated: February 2026*
