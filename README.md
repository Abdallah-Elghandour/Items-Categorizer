# Purchase Order Item Categorizer

## Problem Statement

The dataset consisted of uncategorized purchase orders with item descriptions in multiple languages (primarily Arabic and English). These items needed to be automatically categorized for better spend analysis and procurement insights. Manual categorization was time consuming.

## Architecture & Solution Overview

I developed an automated categorization system using a four-stage approach:
**Multilingual Embeddings -> HDBSCAN Clustering -> LLM -> Rule-based Refinement**


## Setup & Installation


### Installation
```bash
# Clone repository
https://github.com/Abdallah-Elghandour/Items-Categorizer.git

```
- **Python**: 3.11+

```bash
# Create and activate a virtual environment
python3 -m venv venv
source venv/bin/activate   # On Windows use: venv\Scripts\activate
```
```bash
# Install dependencies
pip install -r requirements.txt
```

## Problem-Solving Journey

### 1. Data Exploration & Cleaning

**Challenge**: Raw purchase order data contained missing values and inconsistent formatting.

**Solution**: 
- Loaded data from [purchase-order-items.xlsx]
- Removed rows with NaN values
- Converted data types for consistency
- Final dataset: 2,845 items across 10 columns


### 2. Multilingual Text Processing
**Challenge**: Item descriptions were in Arabic, English, and mixed languages, making traditional NLP approaches insufficient.  

**Solution**:  
- Used **LaBSE (Language-agnostic BERT Sentence Embeddings)**  
- Handles **109+ languages** including Arabic  
- Creates **dense vector representations** preserving semantic meaning  
- Generated **768-dimensional embeddings** for each item  


### 3. HDBSCAN Clustering
**Challenge**: High-dimensional embeddings (768D) needed to be clustered effectively.  

**Solution**: Adopted a two-stage approach:  
- **UMAP**: Reduced embeddings to 15 dimensions while preserving local structure  
- **HDBSCAN**: Applied density-based clustering to handle varying cluster sizes  

**Results**:  
- 27 clusters  
- 601 outliers  


### 4. Visualization for Validation
**Challenge**: Needed to validate that clusters made semantic sense.  

**Solution**: Multi-dimensional visualization  
- **2D visualizations** using PCA and t-SNE

<img width="1331" height="538" alt="image" src="https://github.com/user-attachments/assets/246a0c98-56ff-4cbe-bb16-eeb3d675d56c" />
 
- **3D interactive plots** with Plotly  

<img width="1067" height="717" alt="image" src="https://github.com/user-attachments/assets/95516c53-90f7-4bb3-93e7-06ede33bab93" />


**Results**:  
- Visualizations confirmed that similar items were grouped together



### 5. Automated Labeling with LLM
**Challenge**: Clusters generated from embeddings did not have meaningful category labels, making interpretation difficult.  

**Solution**: Used **DeepSeek API** for automatic categorization  
- Sampled random 10 representative items from each cluster  
- Sent items to LLM with a specific prompt for category identification  
- Generated **initial category labels** for all clusters  


### 6. Rule-Based Refinement
**Challenge**: LLM-generated categories were doublicated and inconsistent.  

**Solution**: Applied **business rules** to create standardized, general categories  
- Mapped similar categories together (e.g., "Steel reinforcement bars", "Reinforcing steel bars" → "Steel Products")  
- Created **7 main business-relevant categories**  
- Maintained traceability to original LLM categories  

**File Generated**:  
- `categorized_purchase_orders.xlsx` – Complete dataset with assigned categories  

### 7. Category Distribution & Key Insights


| Category               | Total Spend  | Items | Avg Spend/Item | % of Total Spend |
|------------------------|-------------|-------|----------------|-----------------|
| Steel Products         | $66.0M      | 928   | $71,155        | 48.4%           |
| Construction Materials | $32.1M      | 594   | $54,072        | 23.5%           |
| Uncategorized          | $28.8M      | 601   | $47,902        | 21.1%           |
| Electrical Products    | $6.04M      | 393   | $15,358        | 4.4%            |
| Metal Products         | $2.46M      | 117   | $20,998        | 1.8%            |
| Plumbing Products      | $0.74M      | 179   | $4,136         | 0.5%            |
| Automotive Products    | $0.26M      | 33    | $7,763         | 0.2%            |

<img width="1269" height="499" alt="image" src="https://github.com/user-attachments/assets/ad84aef4-dd10-461d-9101-bb5384e3d0a0" />
<img width="1330" height="495" alt="image" src="https://github.com/user-attachments/assets/29a18013-360e-44dd-a62e-546653884895" />


**Key Insights**:  
- **Top Spend Categories**: Steel Products and Construction Materials dominate, accounting for ~72% of total spend.  
- **Significant Uncategorized Spend**: 21.1% of spend remains uncategorized, highlighting areas for further refinement.  
- **Portfolio Overview**: Total procurement value of **$136.43M across 2,845 items**.  
- **High-Value vs. Volume**: Steel Products have the highest total spend and item count, while Construction Materials have a high average spend per item.



### 8. Future Enhancements

To improve scalability, accuracy, and usability in a production system:  
- **Active Learning**: builds a feedback loop where procurement experts review uncategorized items, and the system learns from these corrections.  
- **Vector Database Integration**: store embeddings in a vector database (like FAISS or Pinecone) for real-time categorization and similarity search.  
- **LLM Fine-Tuning**: fine-tune or distill models on procurement-specific data to make labeling more consistent and cheaper at scale. 
- **Interactive Dashboard**: provides a user interface for business users to explore clusters, refine categories, and audit decisions.

  
### Usage

1. **Add API Key**:  
   - Add your **DeepSeek API key** to the `.env` file.

2. **Prepare Data**:  
   - Place your purchase order data in `purchase-order-items.xlsx`.

3. **Run Categorizer**:  
   - Open `categorizer.ipynb` and run all cells.

4. **Review Outputs**:  
   - Check `categorized_purchase_orders.xlsx` for the final categorized dataset.  
   
