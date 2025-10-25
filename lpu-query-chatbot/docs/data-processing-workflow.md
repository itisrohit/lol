# 🔧 Data Processing Workflow for LPU Query Chatbot

## 🎯 Processing Objectives

Transform raw student query data into a clean, structured, and analysis-ready dataset for RAG system training, ensuring high-quality input for the chatbot.

---

## 📊 Data Processing Pipeline Overview

```mermaid
flowchart TD
    RawData[Raw Form Data] --> Import[Import Data]
    Import --> InitialQC[Initial Quality Check]
    InitialQC --> DataCleaning[Data Cleaning]
    DataCleaning --> Normalization[Text Normalization]
    Normalization --> Deduplication[De-duplication]
    Deduplication --> Categorization[Query Categorization]
    Categorization --> AnswerCreation[Answer Creation]
    AnswerCreation --> Validation[Quality Validation]
    Validation --> FinalDataset[Final Processed Dataset]
    
    subgraph "Quality Control Points"
        InitialQC --> QCMetrics1[QC Metrics 1]
        DataCleaning --> QCMetrics2[QC Metrics 2]
        Validation --> QCMetricsFinal[Final QC Report]
    end
    
    style RawData fill:#fff3e0
    style FinalDataset fill:#e8f5e8
    style QCMetricsFinal fill:#e1f5fe
```

---

## 🧹 Phase 1: Data Import & Initial Quality Check

### Import Procedures

```mermaid
flowchart TD
    Start([Start Import]) --> SelectSource[Select Data Source]
    SelectSource --> CSVImport[Import CSV/Excel]
    CSVImport --> DataValidation[Validate Data Structure]
    DataValidation --> CheckHeaders{Headers Valid?}
    CheckHeaders -->|No| FixHeaders[Fix Header Issues]
    CheckHeaders -->|Yes| CheckData{Data Format Valid?}
    CheckData -->|No| CleanData[Clean Data Format]
    CheckData -->|Yes| StoreRaw[Store Raw Backup]
    StoreRaw --> GenerateReport[Generate Initial Report]
    GenerateReport --> Complete([Import Complete])
    
    FixHeaders --> DataValidation
    CleanData --> DataValidation
    
    style Start fill:#e1f5fe
    style Complete fill:#e8f5e8
    style StoreRaw fill:#fff3e0
```

### Quality Metrics to Track

| Metric | Target | Measurement |
|--------|--------|-------------|
| Completeness | ≥95% | % of non-empty required fields |
| Valid Categories | 100% | % of valid category selections |
| Question Length | ≥10 chars | % meeting minimum length |
| Duplicate Rate | ≤5% | % of duplicate entries |
| Invalid Data | ≤2% | % of malformed entries |

### Data Validation Rules

```python
# Validation Rules Pseudocode
validation_rules = {
    "question_text": {
        "min_length": 10,
        "max_length": 1000,
        "required": True,
        "no_profanity": True
    },
    "program_department": {
        "allowed_values": program_list,
        "required": True
    },
    "year_of_study": {
        "allowed_values": ["1st Year", "2nd Year", "3rd Year", "4th Year", "5th Year"],
        "required": True
    },
    "question_category": {
        "allowed_values": category_list,
        "required": True
    }
}
```

---

## 🧼 Phase 2: Data Cleaning & Normalization

### Cleaning Workflow

```mermaid
flowchart TD
    RawEntries[Raw Data Entries] --> RemoveEmpty[Remove Empty Entries]
    RemoveEmpty --> RemoveDuplicates[Remove Exact Duplicates]
    RemoveDuplicates --> FixTypos[Fix Common Typos]
    FixTypos --> StandardizeFormat[Standardize Formats]
    StandardizeFormat --> ValidateQC[Validate Cleaning Results]
    ValidateQC --> CleanedData[Cleaned Dataset]
    
    subgraph "Standardization Steps"
        StandardizeFormat --> Lowercase[Convert to Lowercase]
        Lowercase --> TrimSpaces[Trim Extra Spaces]
        TrimSpaces --> StandardPunctuation[Standardize Punctuation]
        StandardPunctuation --> ExpandAbbreviations[Expand Abbreviations]
    end
    
    style RawEntries fill:#fff3e0
    style CleanedData fill:#e8f5e8
    style RemoveDuplicates fill:#ffcdd2
```

### Text Normalization Pipeline

#### 1. Basic Text Cleaning
- Convert to lowercase
- Remove extra whitespace
- Standardize punctuation
- Remove special characters (except meaningful ones)

#### 2. Common LPU-Specific Normalizations
```python
normalization_dict = {
    # LPU specific terms
    "ums": "UMS",
    "lpu connect": "LPU Connect",
    "id card": "ID card",
    "hostel warden": "hostel warden",
    
    # Academic terms
    "bt": "B.Tech",
    "btech": "B.Tech",
    "mtech": "M.Tech",
    "bca": "BCA",
    "mca": "MCA",
    
    # Common typos
    "admisson": "admission",
    "examn": "exam",
    "fees": "fee",
    "libary": "library"
}
```

#### 3. Advanced Processing
- Spell checking using context-aware algorithms
- Slang and informal language mapping
- Abbreviation expansion
- Contextual phrase standardization

### De-duplication Strategy

```mermaid
flowchart TD
    PotentialDuplicates[Potential Duplicates] --> ExactMatch[Exact Text Match]
    ExactMatch --> RemoveExact[Remove Exact Duplicates]
    
    PotentialDuplicates --> SemanticMatch[Semantic Similarity]
    SemanticMatch --> SimilarityThreshold{Similarity > 0.9?}
    SimilarityThreshold -->|Yes| ManualReview[Manual Review]
    SimilarityThreshold -->|No| KeepOriginal[Keep as Separate]
    
    ManualReview --> MergeDecision{Merge?}
    MergeDecision -->|Yes| MergeEntries[Merge Entries]
    MergeDecision -->|No| KeepSeparate[Keep Separate]
    
    RemoveExact --> CleanDataset[Clean Dataset]
    MergeEntries --> CleanDataset
    KeepOriginal --> CleanDataset
    KeepSeparate --> CleanDataset
    
    style PotentialDuplicates fill:#fff3e0
    style CleanDataset fill:#e8f5e8
    style ManualReview fill:#ffcdd2
```

---

## 📂 Phase 3: Query Categorization & Enhancement

### Automated Categorization

```mermaid
flowchart TD
    QueryText[Query Text] --> Preprocessing[Text Preprocessing]
    Preprocessing --> FeatureExtraction[Feature Extraction]
    FeatureExtraction --> MLModel[ML Classification Model]
    MLModel --> CategoryPrediction[Predicted Category]
    CategoryPrediction --> ConfidenceScore{Confidence > 0.8?}
    ConfidenceScore -->|Yes| AutoAssign[Auto-Assign Category]
    ConfidenceScore -->|No| ManualReview[Manual Review Required]
    
    subgraph "Feature Extraction"
        FeatureExtraction --> TFIDF[TF-IDF Vectors]
        FeatureExtraction --> Ngrams[N-gram Analysis]
        FeatureExtraction --> Keywords[Keyword Matching]
    end
    
    AutoAssign --> ValidatedCategory[Validated Category]
    ManualReview --> ValidatedCategory
    
    style QueryText fill:#e1f5fe
    style ValidatedCategory fill:#e8f5e8
    style ManualReview fill:#ffcdd2
```

### Category Refinement Process

#### Primary Categories (21 total)
1. **Academics & Courses** - Course-related queries
2. **Admissions & Applications** - Admission procedures
3. **Exams & Results** - Examination processes and results
4. **Fees & Payments** - Financial matters
5. **Hostel & Accommodation** - Hostel-related queries
6. **Transport & Parking** - Transportation queries
7. **Placements & Internships** - Career-related queries
8. **Campus Facilities** - General campus facilities
9. **Library Services** - Library-specific queries
10. **Sports & Recreation** - Sports and recreational activities
11. **Student Clubs & Activities** - Extracurricular activities
12. **Certificates & Documents** - Document requests
13. **IT Services & Support** - Technical support
14. **Health Services** - Medical facilities
15. **Career Guidance** - Career counseling
16. **Scholarships & Financial Aid** - Financial assistance
17. **Exam Schedule** - Examination schedules
18. **Timetable & Classes** - Class schedules
19. **Attendance Requirements** - Attendance policies
20. **Project Work & Thesis** - Academic projects
21. **General Information** - Miscellaneous queries

### Sub-category Creation

For each primary category, create sub-categories for better organization:

```mermaid
graph TD
    Academics[Academics & Courses] --> Sub1[Course Registration]
    Academics --> Sub2[Course Changes]
    Academics --> Sub3[Academic Calendar]
    Academics --> Sub4[Faculty Queries]
    Academics --> Sub5[Academic Policies]
    
    Exams[Exams & Results] --> SubExam1[Exam Schedule]
    Exams --> SubExam2[Exam Preparation]
    Exams --> SubExam3[Result Queries]
    Exams --> SubExam4[Revaluation]
    Exams --> SubExam5[Exam Rules]
```

---

## ✍️ Phase 4: Answer Creation & Validation

### Answer Creation Workflow

```mermaid
flowchart TD
    CategorizedQuery[Categorized Query] --> ResearchPhase[Research Phase]
    ResearchPhase --> OfficialSources[Check Official Sources]
    OfficialSources --> ConsultExperts[Consult Department Experts]
    ConsultExperts --> DraftAnswer[Draft Answer]
    DraftAnswer --> ReviewProcess[Review Process]
    
    ReviewProcess --> FactCheck[Fact Verification]
    FactCheck --> LanguageReview[Language & Tone Check]
    LanguageReview --> FormatReview[Formatting Review]
    FormatReview --> Approval[Approval Workflow]
    
    Approval --> FinalAnswer[Final Validated Answer]
    FinalAnswer --> AddToDataset[Add to Dataset]
    
    style CategorizedQuery fill:#e1f5fe
    style FinalAnswer fill:#e8f5e8
    style ResearchPhase fill:#fff3e0
    style Approval fill:#ffcdd2
```

### Answer Quality Standards

#### Content Requirements
- **Accuracy**: 100% factually correct
- **Completeness**: Addresses the full question
- **Clarity**: Easy to understand language
- **Conciseness**: Direct and to the point
- **Actionability**: Provides clear next steps

#### Format Guidelines
- **Length**: 50-200 words typically
- **Structure**: Clear beginning, middle, end
- **Tone**: Helpful, professional, student-friendly
- **Contact Info**: Include relevant contact details when needed

#### Answer Template
```markdown
**Direct Answer**: [Clear, direct response to the question]

**Process Steps**:
1. [Step 1 if applicable]
2. [Step 2 if applicable]
3. [Step 3 if applicable]

**Additional Information**:
- [Relevant details]
- [Contact information]
- [Related resources]

**Important Notes**:
- [Warnings or special considerations]
```

### Quality Validation Checklist

```mermaid
flowchart TD
    AnswerDraft[Answer Draft] --> ContentCheck{Content Accurate?}
    ContentCheck -->|No| ReviseContent[Revise Content]
    ContentCheck -->|Yes| LengthCheck{Length Appropriate?}
    LengthCheck -->|No| AdjustLength[Adjust Length]
    LengthCheck -->|Yes| ClarityCheck{Clear & Understandable?}
    ClarityCheck -->|No| ImproveClarity[Improve Clarity]
    ClarityCheck -->|Yes| FormatCheck{Proper Format?}
    FormatCheck -->|No| FixFormat[Fix Formatting]
    FormatCheck -->|Yes| ContactInfo{Contact Info Included?}
    ContactInfo -->|No| AddContact[Add Contact Details]
    ContactInfo -->|Yes| FinalApproval[Final Approval]
    
    ReviseContent --> ContentCheck
    AdjustLength --> LengthCheck
    ImproveClarity --> ClarityCheck
    FixFormat --> FormatCheck
    AddContact --> ContactInfo
    
    style AnswerDraft fill:#fff3e0
    style FinalApproval fill:#e8f5e8
    style ReviseContent fill:#ffcdd2
```

---

## 📊 Phase 5: Final Dataset Assembly

### Dataset Structure

```json
{
  "dataset_info": {
    "version": "1.0",
    "created_date": "2024-01-15",
    "total_entries": 500,
    "categories": 21,
    "last_updated": "2024-01-15"
  },
  "entries": [
    {
      "id": "query_001",
      "original_query": "How to apply for duplicate ID card?",
      "normalized_query": "how to apply for duplicate id card",
      "category": "Certificates & Documents",
      "subcategory": "Student ID",
      "answer": "Visit Block 32, Admin Section with your student ID proof and ₹200 fee. Processing takes 2 working days.",
      "metadata": {
        "department": "All Departments",
        "year": "All Years",
        "frequency": "Often",
        "difficulty": "Easy",
        "confidence_score": 0.95,
        "created_date": "2024-01-10",
        "reviewed_by": "Admin Team",
        "last_updated": "2024-01-12"
      }
    }
  ],
  "statistics": {
    "category_distribution": {
      "Academics & Courses": 85,
      "Exams & Results": 72,
      "Hostel & Accommodation": 63,
      "Certificates & Documents": 58,
      "Fees & Payments": 52,
      "Other Categories": 170
    },
    "quality_metrics": {
      "average_confidence": 0.87,
      "completeness_rate": 0.98,
      "accuracy_estimate": 0.92
    }
  }
}
```

### Quality Assurance Process

```mermaid
flowchart TD
    AssembledDataset[Assembled Dataset] --> DataIntegrityCheck[Data Integrity Check]
    DataIntegrityCheck --> SchemaValidation[Schema Validation]
    SchemaValidation --> CompletenessAudit[Completeness Audit]
    CompletenessAudit --> AccuracyReview[Accuracy Review]
    AccuracyReview --> PerformanceTesting[Performance Testing]
    
    DataIntegrityCheck --> Issues{Issues Found?}
    SchemaValidation --> Issues
    CompletenessAudit --> Issues
    AccuracyReview --> Issues
    PerformanceTesting --> Issues
    
    Issues -->|Yes| FixIssues[Fix Identified Issues]
    Issues -->|No| FinalValidation[Final Validation]
    
    FixIssues --> DataIntegrityCheck
    FinalValidation --> DeployReady[Dataset Ready for Deployment]
    
    style AssembledDataset fill:#fff3e0
    style DeployReady fill:#e8f5e8
    style FixIssues fill:#ffcdd2
```

---

## 📈 Processing Metrics & KPIs

### Processing Efficiency Metrics

| Metric | Target | Measurement Frequency |
|--------|--------|----------------------|
| Processing Speed | 1000 queries/hour | Real-time |
| Error Rate | <1% | Per batch |
| Manual Review Rate | <20% | Per batch |
| Quality Score | >0.85 | Per dataset |

### Dataset Quality Metrics

| Metric | Target | Acceptance Criteria |
|--------|--------|-------------------|
| Category Accuracy | >95% | Correct categorization |
| Answer Accuracy | 100% | Factually correct |
| Completeness | >98% | All required fields filled |
| Consistency | >90% | Uniform format and style |

### Continuous Improvement Metrics

```mermaid
graph LR
    InitialMetrics[Initial Processing Metrics] --> Benchmark[Establish Baseline]
    Benchmark --> OngoingMonitoring[Ongoing Monitoring]
    OngoingMonitoring --> IdentifyIssues[Identify Issues]
    IdentifyIssues --> OptimizeProcess[Optimize Process]
    OptimizeProcess --> ImprovedMetrics[Improved Metrics]
    ImprovedMetrics --> UpdateBenchmark[Update Benchmarks]
    UpdateBenchmark --> OngoingMonitoring
    
    style InitialMetrics fill:#e1f5fe
    style ImprovedMetrics fill:#e8f5e8
    style OptimizeProcess fill:#fff3e0
```

---

## 🛠 Tools & Technologies

### Required Software Stack

#### Data Processing
- **Python 3.8+**: Primary programming language
- **Pandas**: Data manipulation and analysis
- **NumPy**: Numerical computations
- **NLTK/spaCy**: Natural language processing
- **scikit-learn**: Machine learning for categorization
- **fuzzywuzzy**: Fuzzy string matching

#### Quality Assurance
- **pytest**: Unit testing
- **Great Expectations**: Data validation
- **pre-commit**: Code quality checks

#### Data Storage
- **SQLite/PostgreSQL**: Structured data storage
- **JSON**: Dataset export format
- **CSV**: Raw data import/export

### Processing Pipeline Architecture

```mermaid
graph TB
    subgraph "Input Layer"
        CSVFiles[CSV Files] --> DataIngestion[Data Ingestion Service]
        GoogleSheets[Google Sheets] --> DataIngestion
    end
    
    subgraph "Processing Layer"
        DataIngestion --> CleaningService[Cleaning Service]
        CleaningService --> NormalizationService[Normalization Service]
        NormalizationService --> CategorizationService[Categorization Service]
        CategorizationService --> ValidationService[Validation Service]
    end
    
    subgraph "Storage Layer"
        ValidationService --> ProcessedDB[(Processed Database)]
        ValidationService --> RawDB[(Raw Data Backup)]
        ValidationService --> LogsDB[(Processing Logs)]
    end
    
    subgraph "Output Layer"
        ProcessedDB --> JSONExport[JSON Export]
        ProcessedDB --> APIEndpoint[API Endpoint]
        ProcessedDB --> Analytics[Analytics Dashboard]
    end
    
    style CSVFiles fill:#e1f5fe
    style JSONExport fill:#e8f5e8
    style ProcessedDB fill:#f3e5f5
```

---

## 🔄 Maintenance & Updates

### Ongoing Data Maintenance

```mermaid
flowchart TD
    NewData[New Raw Data] --> IncrementalProcessing[Incremental Processing]
    IncrementalProcessing --> MergeWithExisting[Merge with Existing Dataset]
    MergeWithExisting --> Revalidate[Re-validate Combined Dataset]
    Revalidate --> UpdateVersion[Update Dataset Version]
    UpdateVersion --> RetrainModels[Retrain ML Models]
    RetrainModels --> DeployUpdate[Deploy Updated System]
    
    style NewData fill:#e1f5fe
    style DeployUpdate fill:#e8f5e8
```

### Version Control Strategy

- **Semantic Versioning**: MAJOR.MINOR.PATCH
- **Backup Strategy**: Keep last 5 versions
- **Rollback Plan**: Ability to revert to previous versions
- **Change Logs**: Detailed documentation of all changes

### Quality Monitoring Dashboard

Track the following metrics in real-time:
- Processing speed and throughput
- Error rates and types
- Quality scores over time
- Category distribution changes
- User feedback on answers

This comprehensive data processing workflow ensures that the LPU Query Chatbot is built on a foundation of high-quality, well-structured data that continuously improves over time.
