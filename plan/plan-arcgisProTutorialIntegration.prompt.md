# Plan: Integrate ArcGIS Pro STAC Tutorial

## Overview

Complete integration of the ArcGIS Pro tutorial for accessing STAC DEM mosaics, including screenshot placement, content enhancement, navigation registration, and bilingual documentation with English-first validation workflow.

## Current Status

### Existing Assets
- ✅ English tutorial draft: `docs/en/stac-dem-mosaics/accessing-stac-with-arcpro.md` (145 lines)
- ✅ 19 ArcGIS Pro-specific screenshots in `docs/assets/images/` (all start with `stac-arcpro-`)
- ❌ Not registered in `mkdocs.yml` navigation
- ❌ Not mentioned in overview pages (`index.md`)
- ❌ French version missing
- ❌ No images embedded in current draft

### Quality Issues in Draft
- **Missing images**: 0 images embedded despite 19 available screenshots
- **Prerequisites error**: References "QGIS STAC API Browser Plugin" (copy-paste error from QGIS tutorial)
- **Incomplete troubleshooting**: Less detailed than QGIS/Python tutorials
- **Missing sections**: No glossary, no additional resources/external references

## Available Screenshots

### Connection Setup (Part 1)
1. `stac-arcpro-new-stac-connection.png` - Insert > Project > Connections dialog
2. `stac-arcpro-create-stac-connection.png` - STAC connection configuration dialog
3. `stac-arcpro-stac-connection-in-catalog.png` - STAC connection appearing in Catalog pane

### Collection Discovery (Part 2)
4. `stac-arcpro-catalog-explore-stac.png` - Right-click context menu with "Explore STAC..." option
5. `stac-arcpro-collection-pane.png` - Collections list in Explore STAC pane
6. `stac-arcpro-search-collection-bar.png` - Search Collections bar interface
7. `stac-arcpro-search-collection-bar-dem.png` - Search Collections bar with DEM search

### Asset Selection (Part 3)
8. `stac-arcpro-explore-stac-select-assets.png` - Select assets button/dialog
9. `stac-arcpro-explore-stac-view-assets.png` - Asset viewing pane

### Filtering and Search (Part 3)
10. `stac-arcpro-search-filtering-options.png` - Filtering options overview
11. `stac-arcpro-filtering-current-display.png` - Current Display Extent option
12. `stac-arcpro-filtering-extents-manually.png` - Manual extent entry fields

### Results Navigation (Part 3)
13. `stac-arcpro-explore-stac-results.png` - Search results list
14. `stac-arcpro-explore-stac-results-item-thumbnail.png` - Item thumbnail preview
15. `stac-arcpro-explore-stac-results-item-footprint.png` - Item footprint on map
16. `stac-arcpro-explore-stac-results-item-metadata.png` - Item metadata properties
17. `stac-arcpro-explore-stac-items-per-page.png` - Items per page configuration

### Loading and Export (Part 3-4)
18. `stac-arcpro-load-dem-current-map.png` - "Add to Current Map" option
19. `stac-arcpro-export-raster-panel.png` - Export Raster dialog

## Implementation Steps

### Step 1: Enhance English Tutorial Content

**File**: `docs/en/stac-dem-mosaics/accessing-stac-with-arcpro.md`

#### 1.1 Fix Prerequisites Section
- Remove incorrect "QGIS STAC API Browser Plugin" line
- Verify ArcGIS Pro version requirement (currently states 3.4.0+)
- Ensure consistency with tutorial content

#### 1.2 Insert Screenshots Throughout Tutorial

**Part 1: Plugin Installation and Configuration**
- After step 3 "Create a new STAC connection": Insert `stac-arcpro-new-stac-connection.png`
- After step 4 "Enter connection details": Insert `stac-arcpro-create-stac-connection.png`
- After step 6 "In the Catalog pane...": Insert `stac-arcpro-stac-connection-in-catalog.png`

**Part 2: Discovering DEM Collections**
- Before "Browse Available Collections": Insert `stac-arcpro-catalog-explore-stac.png`
- After "Use the Search Collections bar": Insert `stac-arcpro-search-collection-bar.png` or `stac-arcpro-search-collection-bar-dem.png`

**Part 3: Searching and Loading DEM Data**
- After "Select desired assets": Insert `stac-arcpro-explore-stac-select-assets.png`
- Before "Search by Geographic Extent": Insert `stac-arcpro-search-filtering-options.png`
- After "Method A: Use Map Canvas Extent": Insert `stac-arcpro-filtering-current-display.png`
- After "Method B: Define Extents Manually": Insert `stac-arcpro-filtering-extents-manually.png`
- After "Search results appear": Insert `stac-arcpro-explore-stac-results.png`
- After "Thumbnail: Preview image": Insert `stac-arcpro-explore-stac-results-item-thumbnail.png`
- After "Footprint: Geographic extent": Insert `stac-arcpro-explore-stac-results-item-footprint.png`
- After "Metadata: Properties": Insert `stac-arcpro-explore-stac-results-item-metadata.png`
- After "Items per page": Insert `stac-arcpro-explore-stac-items-per-page.png`
- After "Load DEM Tiles" step 1: Insert `stac-arcpro-load-dem-current-map.png`

**Part 4: Exporting and Offline Use**
- After "Configure export settings": Insert `stac-arcpro-export-raster-panel.png`

**Image Reference Format**: 
```markdown
![Alt text describing screenshot](../assets/images/stac-arcpro-filename.png)
```

#### 1.3 Expand Troubleshooting Section

Add more detailed troubleshooting scenarios:
- **Cannot see STAC connection in Catalog**: Check ArcGIS Pro version, restart application
- **No collections appearing**: Verify internet connection, check STAC API status
- **COG tiles fail to load**: Check file format support, verify URL accessibility. Also specify that only the dem collections can be loaded. There is a bug preventing other collections of the STAC catalog from loading.
- **Performance issues**: Tips for working with large datasets, caching strategies
- **Export failures**: Disk space, permissions, output format compatibility

#### 1.4 Add Glossary Section

Match pattern from QGIS and Python tutorials. Include:
- **STAC** (SpatioTemporal Asset Catalog)
- **COG** (Cloud Optimized GeoTIFF)
- **DEM** (Digital Elevation Model)
- **HRDEM** (High Resolution Digital Elevation Model)
- **MRDEM** (Medium Resolution Digital Elevation Model)
- **CCMEO** (Canadian Centre for Mapping and Earth Observation)
- **AOI** (Area of Interest)
- **CRS** (Coordinate Reference System)

Format:
```markdown
---

## Glossary

- **STAC (SpatioTemporal Asset Catalog):** [Definition]
...
```

#### 1.5 Add External Resources Section

Include:
- Official ArcGIS Pro STAC documentation links
- CCMEO STAC API documentation
- COG specification resources

Format:
```markdown
---

## Additional Resources

- [ArcGIS Pro STAC Documentation](URL) ↗️
- [CCMEO STAC API](https://datacube.services.geo.ca/stac/api/) ↗️
...
```

#### 1.6 Improve Content Flow

- Add introductory context about why ArcGIS Pro for STAC access
- Enhance transition text between sections
- Ensure consistent terminology throughout

### Step 2: Register Tutorial in Site Navigation

**File**: `mkdocs.yml`

#### 2.1 Add to Main Navigation (around line 84)

Current structure:
```yaml
  - STAC DEM Mosaics:
    - Overview: stac-dem-mosaics/index.md
    - Accessing STAC with Python: stac-dem-mosaics/accessing-stac-with-python.md
    - Accessing STAC with QGIS: stac-dem-mosaics/accessing-stac-with-qgis.md
```

Add:
```yaml
  - STAC DEM Mosaics:
    - Overview: stac-dem-mosaics/index.md
    - Accessing STAC with Python: stac-dem-mosaics/accessing-stac-with-python.md
    - Accessing STAC with QGIS: stac-dem-mosaics/accessing-stac-with-qgis.md
    - Accessing STAC with ArcGIS Pro: stac-dem-mosaics/accessing-stac-with-arcpro.md
```

#### 2.2 Add French Translation (around line 54)

Current structure:
```yaml
nav_translations:
  ...
  Accessing STAC with Python: Accès à STAC avec Python
  Accessing STAC with QGIS: Accès à STAC avec QGIS
```

Add:
```yaml
  Accessing STAC with ArcGIS Pro: Accès à STAC avec ArcGIS Pro
```

### Step 3: Update Overview Pages

#### 3.1 English Overview

**File**: `docs/en/stac-dem-mosaics/index.md`

Add ArcGIS Pro tutorial to the introduction/summary section where Python and QGIS tutorials are mentioned. Maintain consistent format with existing entries.

Expected pattern (based on file structure):
- Brief description of what ArcGIS Pro offers
- Link to tutorial
- Mention of visual/interactive workflow
- Target audience (ArcGIS Pro users, ESRI ecosystem users)

#### 3.2 French Overview

**File**: `docs/fr/stac-dem-mosaics/index.md`

Add corresponding French description of ArcGIS Pro tutorial, mirroring the English structure but in French.

### Step 4: Validation Checkpoint

**USER ACTION REQUIRED**

Before proceeding to French translation, user should review and validate:

1. **Technical Accuracy**
   - [ ] Prerequisites are correct (ArcGIS Pro version, requirements)
   - [ ] Step-by-step instructions are accurate and complete
   - [ ] STAC API URL is correct
   - [ ] Collection names and descriptions are accurate

2. **Screenshot Placement**
   - [ ] All 19 screenshots are placed appropriately
   - [ ] Screenshots match the described steps
   - [ ] Alt text is descriptive and helpful
   - [ ] Image paths are correct (`../assets/images/`)

3. **Content Completeness**
   - [ ] All sections are present (Prerequisites, Learning Objectives, Parts 1-4, Troubleshooting, Best Practices, Glossary, Additional Resources)
   - [ ] Troubleshooting covers common issues
   - [ ] Glossary matches other tutorials
   - [ ] External resources are relevant and accessible

4. **Navigation and Integration**
   - [ ] Tutorial appears in site navigation (test with `mkdocs serve`)
   - [ ] Tutorial listed on overview pages (EN)
   - [ ] Links work correctly
   - [ ] Rendering is correct (no broken images, formatting issues)

5. **Consistency with Other Tutorials**
   - [ ] Structure aligns with QGIS/Python tutorials
   - [ ] Tone and style are consistent
   - [ ] Formatting patterns match
   - [ ] Similar depth and detail

### Step 5: Create French Translation

**ONLY AFTER STEP 4 VALIDATION IS COMPLETE**

**File**: `docs/fr/stac-dem-mosaics/accessing-stac-with-arcpro.md` (NEW)

#### 5.1 Translation Guidelines

**Translate:**
- Page title and introduction
- Section headings (## and ###)
- Body text and instructions
- Troubleshooting content
- Best practices
- Glossary definitions
- Alt text for images
- Acronyms that are translated in other French tutorials (e.g., DEM = MNE)

**Keep in English:**
- URLs and hyperlinks
- Code blocks and technical commands
- Software version numbers
- Collection names (hrdem-mosaic-1m, etc.)
- Technical acronyms (STAC, COG) - only translate the definitions in glossary
- Image filenames and paths
- API endpoints
- **UI Element Names and Function Names:** All ArcGIS Pro interface elements, menus, panels, buttons, and field names MUST remain in English (e.g., "Catalog pane", "View > Catalog Pane", "Explore STAC", "Select assets", "Map Canvas Extent", "Current Display Extent", "Results", "Parameters", "Items per page", etc.). This ensures users can match the French text with the English UI they see in ArcGIS Pro. Follow the same pattern as the French QGIS tutorial.

**Standard Translations:**
- "Prerequisites" → "Prérequis"
- "Learning Objectives" → "Objectifs d'apprentissage"
- "Overview" → "Aperçu"
- "Troubleshooting" → "Dépannage"
- "Best Practices" → "Bonnes pratiques"
- "Glossary" → "Glossaire"
- "Additional Resources" → "Ressources supplémentaires"
- "Part 1" → "Partie 1"

#### 5.2 Image References in French

Maintain same image paths, translate only alt text:

English:
```markdown
![STAC Connection Setup](../assets/images/stac-arcpro-create-stac-connection.png)
```

French:
```markdown
![Configuration de la connexion STAC](../assets/images/stac-arcpro-create-stac-connection.png)
```

#### 5.3 Quality Checks for Translation

- [ ] All sections translated
- [ ] Technical terms handled correctly
- [ ] Image paths unchanged
- [ ] URLs unchanged
- [ ] Formatting preserved
- [ ] Structure identical to English version
- [ ] Glossary translations match other French tutorials

### Step 6: Create Plan Document

**File**: `plan/arcgis-pro-tutorial-integration.md` (NEW)

Save this comprehensive plan document to the `plan/` folder for:
- Documentation of integration approach
- Reference for future similar integrations
- Screenshot mapping guide
- Validation checklist
- Translation guidelines

## Screenshot Mapping Reference

| Screenshot File | Tutorial Section | Placement Location |
|----------------|------------------|-------------------|
| `stac-arcpro-new-stac-connection.png` | Part 1 | After "Create a new STAC connection" |
| `stac-arcpro-create-stac-connection.png` | Part 1 | After "Enter connection details" |
| `stac-arcpro-stac-connection-in-catalog.png` | Part 1 | After "In the Catalog pane..." |
| `stac-arcpro-catalog-explore-stac.png` | Part 2 | Before "Browse Available Collections" |
| `stac-arcpro-search-collection-bar.png` | Part 2 | After "Use the Search Collections bar" |
| `stac-arcpro-search-collection-bar-dem.png` | Part 2 | Alternative to above |
| `stac-arcpro-explore-stac-select-assets.png` | Part 3 | After "Select desired assets" |
| `stac-arcpro-search-filtering-options.png` | Part 3 | Before "Search by Geographic Extent" |
| `stac-arcpro-filtering-current-display.png` | Part 3 | After "Method A" description |
| `stac-arcpro-filtering-extents-manually.png` | Part 3 | After "Method B" description |
| `stac-arcpro-explore-stac-results.png` | Part 3 | After "Search results appear" |
| `stac-arcpro-explore-stac-results-item-thumbnail.png` | Part 3 | After "Thumbnail" description |
| `stac-arcpro-explore-stac-results-item-footprint.png` | Part 3 | After "Footprint" description |
| `stac-arcpro-explore-stac-results-item-metadata.png` | Part 3 | After "Metadata" description |
| `stac-arcpro-explore-stac-items-per-page.png` | Part 3 | After "Items per page" mention |
| `stac-arcpro-load-dem-current-map.png` | Part 3 | After "Load DEM Tiles" step |
| `stac-arcpro-export-raster-panel.png` | Part 4 | After "Configure export settings" |

## Success Criteria

### English Version (Step 4 Validation)
- [ ] All 19 screenshots embedded and appropriately placed
- [ ] Prerequisites section corrected (no QGIS references)
- [ ] Troubleshooting section expanded to match other tutorials
- [ ] Glossary section added
- [ ] Additional Resources section added
- [ ] Tutorial registered in `mkdocs.yml` navigation
- [ ] Tutorial listed in English overview page
- [ ] Site builds successfully (`mkdocs build`)
- [ ] Tutorial renders correctly in local preview
- [ ] All links work correctly
- [ ] Technical accuracy verified

### French Version (After Step 5)
- [ ] Complete translation in `docs/fr/stac-dem-mosaics/accessing-stac-with-arcpro.md`
- [ ] Structure mirrors English version exactly
- [ ] Image paths unchanged
- [ ] Alt text translated
- [ ] French navigation translation added to `mkdocs.yml`
- [ ] Tutorial listed in French overview page
- [ ] Site builds successfully with French content
- [ ] French version renders correctly
- [ ] Terminology consistent with other French tutorials

## Timeline Estimate

- **Step 1** (Enhance English): 2-3 hours
- **Step 2** (Navigation): 15 minutes
- **Step 3** (Overview pages): 30 minutes
- **Step 4** (Validation): User-dependent
- **Step 5** (French translation): 2-3 hours
- **Step 6** (Plan document): 15 minutes (completed)

**Total Active Work**: ~5-7 hours (excluding validation wait time)

## Dependencies

- All 19 screenshots must remain in `docs/assets/images/` folder
- MkDocs and plugins must be properly configured (already complete)
- Git branch for changes (recommend: `feature/arcpro-tutorial-integration`)

## Risks and Mitigation

| Risk | Mitigation |
|------|------------|
| Screenshots don't match current ArcGIS Pro UI | Review and update screenshots if needed; note in validation |
| ArcGIS Pro version compatibility | Verify minimum version with latest STAC support |
| STAC API changes | Test connection and document current API version |
| Navigation conflicts | Test build after navigation changes |
| Translation inconsistencies | Use existing French tutorials as reference |

## Notes

- This is the first tutorial requiring French translation after completion
- The English-first, validate, then translate workflow is a new pattern
- All screenshots are already created and named appropriately
- Draft tutorial structure is solid, mainly needs enhancement and images
- Plan folder was previously empty, this establishes documentation pattern
