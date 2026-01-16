# W-WORN — Vinted Worn Photo Generation
## Technical Specification v1.0

---

## 📋 Overview

Sistema automatizzato per generazione di foto "indossate" stile Vinted marketplace.
Crea 5 pose realistiche usando AI image generation con consistency model + ambiente.

### Obiettivi
- Generare foto authentic-looking per Vinted (amateur smartphone style)
- Mantenere consistenza: stessa modella, stessa stanza, stesso capo
- Output: 5 foto + CSV per bulk upload Vinted

---

## 🎯 Feature Requirements

### Input
| Campo | Source | Required |
|-------|--------|----------|
| SKU | INVENTARIO | ✅ |
| AI_Front_Image_Link | INVENTARIO | ✅ |
| AI_Back_Image_Link | INVENTARIO | ✅ |
| Parent_Folder_ID | INVENTARIO | ✅ |
| Gender_Standard | INVENTARIO | ✅ |
| Size_Standard | INVENTARIO | ✅ |
| Category | INVENTARIO | ✅ |
| SubCategory_Clean | INVENTARIO | ✅ |
| Brand_Standard | INVENTARIO | ✅ |
| Color_Primary | INVENTARIO | ✅ |

### Output
- 5 foto PNG/JPG (Google Drive + ImgBB)
- CSV file per Vinted bulk upload
- Airtable fields update

---

## 🎨 5 Poses Configuration

### Pose Matrix

| # | Pose ID | Photo Style | Use Back Image | Face Visibility |
|---|---------|-------------|----------------|-----------------|
| 1 | `selfie_front` | Mirror selfie frontale | No | Partially obscured by phone |
| 2 | `selfie_angled` | Mirror selfie angolato | No | Partially obscured |
| 3 | `candid_back` | Candid vista retro | Yes | Not visible (back to camera) |
| 4 | `candid_side` | Candid profilo laterale | No | Profile view |
| 5 | `detail_closeup` | Detail close-up | No | Not visible (crop) |

### Pose Descriptions

#### 1. Mirror Selfie Frontal
```
Position: Model standing facing mirror
Phone: Held at chest level
Body: Full body visible in reflection
Stance: Relaxed casual
Focus: Garment fully visible front view
```

#### 2. Mirror Selfie Angled
```
Position: Model at 30-45 degree angle to mirror
Phone: Held at face level
Body: Full body visible
Stance: Slight hip tilt, dynamic pose
Focus: Shows garment draping and fit
```

#### 3. Candid Back View
```
Position: Model with back to camera
Setting: Natural room environment
Body: Three-quarter or full body
Stance: Walking away or looking over shoulder
Focus: Back details, fit from behind
```

#### 4. Candid Side Profile
```
Position: Model standing sideways
Setting: Natural room environment
Body: Full body side view
Stance: Natural walking or standing
Focus: Silhouette, how garment hangs
```

#### 5. Detail Close-up
```
Position: Cropped torso shot
Focus: Upper body only
Stance: Hands may adjust garment
Focus: Fabric texture, stitching, logos
```

---

## 👗 Model Reference Selection

### Logic Flow

```
IF Gender = 'Men' OR Gender = 'Unisex' (male-leaning):
    → Model_Male reference

ELSE IF Gender = 'Women' OR Gender = 'Kids':
    IF Size IN ['XXS', 'XS', 'S', '36', '38']:
        → Model_Female_S reference
    ELSE (M, L, XL, XXL, 40+):
        → Model_Female_M reference
```

### Reference File IDs

| Reference | Google Drive File ID | Usage |
|-----------|---------------------|-------|
| Room | `1PEfxkDbUCRX7X-gv3hmnld1ftBKeHmdm` | All poses (environment) |
| Model Female S | `1VZks84n9lOgFQO6cj9UdM5OP_jTPqc1T` | Women/Kids small sizes |
| Model Female M | `1u0Ae9O1edGL-JJNOfHnTw4dusuP9zk0G` | Women medium+ sizes |
| Model Male | `1-nO_qfqckRxqkHQ2Bs5v5irs45TXj7ue` | Men/Unisex items |

### Size Classification

```javascript
const SMALL_SIZES = ['XXS', 'XS', 'S', '34', '36', '38', '40'];
const isSmallSize = SMALL_SIZES.includes(size.toUpperCase());
```

---

## 🤖 AI Generation Pipeline

### Technology
- **Provider:** Google Gemini 2.0 Flash (Preview Image Generation)
- **Model:** `gemini-2.0-flash-preview-image-generation`
- **Input:** Multi-image (3 images: product, model, room)
- **Output:** Single generated image per API call

### API Configuration

```javascript
const GEMINI_CONFIG = {
  endpoint: 'https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash-preview-image-generation:generateContent',
  timeout: 180000, // 3 minutes per image
  retries: 2
};
```

### Prompt Structure

```
SYSTEM: You are generating an authentic Vinted marketplace photo.
Create a photorealistic image that looks like a casual smartphone
photo taken by someone selling clothes from home.

══════════════════════════════════════════════════════════════════
INPUT IMAGES EXPLANATION
══════════════════════════════════════════════════════════════════

IMAGE 1 (GARMENT): This is the exact garment to be worn.
Preserve this garment exactly - colors, textures, prints, logos.

IMAGE 2 (MODEL REFERENCE): This is the person wearing the garment.
Replicate this SAME person - face, hair, body type, skin tone.

IMAGE 3 (ROOM REFERENCE): This is the environment/room setting.
Keep the same room, lighting, and atmosphere.

══════════════════════════════════════════════════════════════════
POSE INSTRUCTION
══════════════════════════════════════════════════════════════════

[POSE-SPECIFIC INSTRUCTION HERE]

══════════════════════════════════════════════════════════════════
STYLE REQUIREMENTS
══════════════════════════════════════════════════════════════════

Photography style:
- Casual smartphone quality (not professional)
- Natural home lighting (slightly warm)
- Mirror selfie or candid vibe
- Authentic Vinted/Depop aesthetic

CRITICAL: The garment must be IDENTICAL to Image 1.
```

---

## 📁 Output Storage

### Google Drive Structure

```
VINTED_EXPORTS/
└── {batch_id}/
    └── {SKU}/
        ├── {SKU}_WORN_01.jpg  (selfie_front)
        ├── {SKU}_WORN_02.jpg  (selfie_angled)
        ├── {SKU}_WORN_03.jpg  (candid_back)
        ├── {SKU}_WORN_04.jpg  (candid_side)
        └── {SKU}_WORN_05.jpg  (detail_closeup)
```

### ImgBB Temporary URLs

**Purpose:** Public URLs for CSV export (Vinted requires direct image URLs)

```javascript
const IMGBB_CONFIG = {
  api_key: 'a948b144653e5d76288abd8602bb1a10',
  expiration: 86400, // 24 hours
  naming: '{SKU}_WORN_{pose_index}'
};
```

**Dual Storage Strategy:**
1. Permanent: Google Drive (internal reference)
2. Temporary: ImgBB (CSV export URLs, 24h validity)

---

## 📊 CSV Output Format

### Vinted Bulk Upload Schema

```csv
title,description,brand,size,color,condition,price,category_id,photo1,photo2,photo3,photo4,photo5
```

### Field Mapping

| CSV Column | Airtable Source | Notes |
|------------|-----------------|-------|
| title | TITLE_IT | Truncate to 80 chars |
| description | DESCRIPTION_IT | Truncate to 2000 chars |
| brand | Brand_Standard | Must match Vinted brand list |
| size | Size_Standard | Mapped to Vinted size_id |
| color | Color_Primary | Mapped to Vinted color_id |
| condition | Condition_Standard | Mapped to Vinted condition_id |
| price | Prezzo_Vinted | EUR amount |
| category_id | Vinted_Category_ID | Pre-mapped category |
| photo1-5 | ImgBB URLs | 24h validity |

### CSV Generation

```javascript
const csvRow = {
  title: record.TITLE_IT?.substring(0, 80) || `${record.Brand_Standard} ${record.SubCategory_Clean}`,
  description: record.DESCRIPTION_IT?.substring(0, 2000) || '',
  brand: record.Brand_Standard,
  size: mapSizeToVinted(record.Size_Standard),
  color: mapColorToVinted(record.Color_Primary),
  condition: mapConditionToVinted(record.Condition_Standard),
  price: record.Prezzo_Vinted,
  category_id: record.Vinted_Category_ID,
  photo1: imgbbUrls[0],
  photo2: imgbbUrls[1],
  photo3: imgbbUrls[2],
  photo4: imgbbUrls[3],
  photo5: imgbbUrls[4]
};
```

---

## ⚡ Workflow Architecture

### Trigger Mode
**Type:** When Executed by Another Workflow (sub-workflow)
**Caller:** Parent orchestration workflow or webapp webhook

### Flow Diagram

```
[Trigger: Execute Workflow]
         │
         ▼
[Validate_Input] ─── Error → [Return Error Response]
         │
         ▼
[Select_Model_Reference] (gender + size logic)
         │
         ▼
[Fetch_Reference_Images]
├── Download Room reference
├── Download Model reference
└── Download Product images (front + back)
         │
         ▼
[Convert_To_Base64]
         │
         ▼
[Split_Into_5_Poses]
         │
         ▼
[Loop: SplitInBatches (size=1)]
         │
    ┌────┴────┐
    │  LOOP   │
    │         │
    ▼         │
[Build_Gemini_Prompt]
    │         │
    ▼         │
[Call_Gemini_API]
    │         │
    ├── Success ──▶ [Extract_Image_Base64]
    │                      │
    └── Error ────▶ [Log_Error]
                           │
    ▼                      │
[Upload_Google_Drive]      │
    │                      │
    ▼                      │
[Upload_ImgBB]             │
    │                      │
    ▼                      │
[Aggregate_Results] ◀──────┘
         │
         ▼
[Build_CSV_Row]
         │
         ▼
[Update_Airtable]
         │
         ▼
[Return_Success_Response]
```

### Parallel Processing

**Rate Limiting:** Sequential (1 pose at a time)
- Gemini API can handle parallel but consistency better with sequential
- ImgBB rate limit: 100 req/minute (not a concern)

---

## 📋 Airtable Updates

### Fields to Update (INVENTARIO)

| Field | Type | Value |
|-------|------|-------|
| Vinted_Worn_Generated | Checkbox | `1` |
| Vinted_Worn_Date | DateTime | ISO timestamp |
| Vinted_Worn_Count | Number | `5` |
| Vinted_CSV_Link | URL | Drive link to CSV |
| Vinted_Folder_Link | URL | Drive folder link |
| Vinted_ImgBB_URLs | Long Text | JSON array of temp URLs |

### New Fields Required

```
INVENTARIO Table:
├── Vinted_Worn_Generated (Checkbox)
├── Vinted_Worn_Date (DateTime)
├── Vinted_Worn_Count (Number)
├── Vinted_CSV_Link (URL)
├── Vinted_Folder_Link (URL)
├── Vinted_ImgBB_URLs (Long Text)
└── Vinted_Worn_Trigger (Checkbox) [optional, for Airtable trigger mode]
```

---

## 🚨 Error Handling

### Error Categories

| Error Type | Action | Retry |
|------------|--------|-------|
| Gemini timeout | Log, skip pose | 2x |
| Gemini content filter | Log, skip pose | No |
| ImgBB upload fail | Use Drive URL only | 2x |
| Invalid input | Return error response | No |
| Missing reference image | Abort workflow | No |

### Error Response

```javascript
{
  success: false,
  error: {
    code: 'GEMINI_TIMEOUT',
    message: 'Image generation timed out for pose 3',
    sku: 'MF-2411',
    pose: 'candid_back'
  },
  partial_results: {
    generated: 2,
    failed: 1,
    urls: [...]
  }
}
```

---

## 📊 Success Response

```javascript
{
  success: true,
  sku: 'MF-2411',
  batch_id: 'worn-2026-01-16-001',
  generated_count: 5,
  storage: {
    google_drive: {
      folder_id: '1abc...',
      folder_url: 'https://drive.google.com/...',
      files: [
        { name: 'MF-2411_WORN_01.jpg', id: '1xyz...' },
        // ...
      ]
    },
    imgbb: {
      expiration: '2026-01-17T15:30:00Z',
      urls: [
        'https://i.ibb.co/abc123/MF-2411_WORN_01.jpg',
        // ...
      ]
    }
  },
  csv: {
    file_id: '1csv...',
    file_url: 'https://drive.google.com/...',
    row_count: 1
  },
  processing_time_ms: 45000
}
```

---

## 🔧 Implementation Checklist

### Phase 1: Core Workflow
- [ ] Create W-WORN workflow skeleton
- [ ] Implement model selection logic
- [ ] Implement reference image fetching
- [ ] Implement Gemini multi-image API call
- [ ] Test with 1 SKU, 1 pose

### Phase 2: Full Pipeline
- [ ] Implement all 5 poses
- [ ] Implement sequential loop
- [ ] Add Google Drive upload
- [ ] Add ImgBB upload
- [ ] Implement aggregation

### Phase 3: Output & Integration
- [ ] Build CSV generation
- [ ] Implement Airtable updates
- [ ] Add error handling
- [ ] Test with 5 SKU batch

### Phase 4: Webapp Trigger
- [ ] Build selection UI
- [ ] Implement webhook trigger
- [ ] Add status polling
- [ ] Test end-to-end

---

## 📚 Related Documentation

- `WORKFLOWS.md` - Main workflow specifications
- `AI_PROMPTS.md` - AI prompt patterns
- `GOOGLE_DRIVE.md` - Storage structure
- `AIRTABLE_SCHEMA.md` - Field definitions

---

**Document Version:** 1.0  
**Date:** 16 Gennaio 2026  
**Status:** 📋 SPECIFICATION READY
