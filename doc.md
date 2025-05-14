# Power BI PBIR Visual.json Documentation

## Overview of PBIR Folder Structure

The Power BI Enhanced Report Format (PBIR) organizes report elements into a structured folder hierarchy to improve version control, collaboration, and maintainability. The main structure includes:

```
definition/
├── bookmarks/
│   ├── [bookmarkName].bookmark.json
│   └── bookmarks.json
├── pages/
│   ├── [pageName]/
│   │   ├── visuals/
│   │   │   ├── [visualName]/
│   │   │   │   ├── mobile.json
│   │   │   │   └── visual.json
│   │   └── page.json
│   └── pages.json
├── version.json
├── reportExtensions.json
└── report.json
```

## Important Files 

1. **definition.pbir**: Contains the overall definition of a report and core settings including the reference to the semantic model.

2. **version.json**: Specifies the PBIR file version (currently 2.0.0 in your project).

3. **report.json**: Contains report-level settings including themes, layout optimization, and global settings.

4. **page.json**: Contains page-specific metadata such as display name, display options, and dimensions.

5. **visual.json**: Contains all the properties and configurations for an individual visual.

## Visual.json File Structure

Each `visual.json` file defines a specific visualization within a Power BI report. From your uploaded files, all visuals have this core structure:

```json
{
  "$schema": "https://developer.microsoft.com/json-schemas/fabric/item/report/definition/visualContainer/1.5.0/schema.json",
  "name": "unique-identifier",
  "position": {
    "x": X-coordinate,
    "y": Y-coordinate,
    "z": Z-index,
    "height": height-value,
    "width": width-value,
    "tabOrder": tab-order-value
  },
  "visual": {
    "visualType": "visual-type",
    "query": {
      "queryState": {
        // Query configuration
      },
      "sortDefinition": {
        // Sorting configuration
      }
    },
    "objects": {
      // Optional visual-specific properties
    },
    "drillFilterOtherVisuals": true/false
  }
}
```

## Key Properties in visual.json

### 1. Top-Level Properties

| Property | Description |
|----------|-------------|
| `$schema` | References the JSON schema that defines the structure of the visual.json file. Your files use version 1.5.0. |
| `name` | Unique identifier for the visual (e.g., "a50be2b7dee494640ce0"). |
| `position` | Defines the visual's position and size on the report page. |
| `visual` | Contains all visual-specific settings including type, data, and formatting. |
| `filterConfig` | Optional property that contains filter settings specific to the visual. |

### 2. Position Properties

| Property | Description |
|----------|-------------|
| `x` | Horizontal position of the visual on the page. |
| `y` | Vertical position of the visual on the page. |
| `z` | Z-index determining the stacking order (higher values appear on top). |
| `height` | Height of the visual in pixels. |
| `width` | Width of the visual in pixels. |
| `tabOrder` | Order of navigation when using the Tab key (usually matches the z-index). |

### 3. Visual Properties

| Property | Description |
|----------|-------------|
| `visualType` | Type of visual (e.g., "barChart", "pieChart", "lineChart"). |
| `query` | Contains data query configuration. |
| `objects` | Optional property containing additional format and behavior settings. |
| `drillFilterOtherVisuals` | Boolean indicating if drilling on this visual filters other visuals. |

### 4. Query Properties

The `query` section defines how data is retrieved and structured for the visual:

```json
"query": {
  "queryState": {
    "Category": {
      "projections": [...]
    },
    "Series": {
      "projections": [...]
    },
    "Y": {
      "projections": [...]
    }
  },
  "sortDefinition": {
    "sort": [...],
    "isDefaultSort": true/false
  }
}
```

| Property | Description |
|----------|-------------|
| `queryState` | Defines data roles (Category, Series, Y, etc.) and their field mappings. |
| `projections` | Arrays of fields mapped to specific data roles. |
| `field` | Defines the data field reference, often including a Column with Expression, SourceRef, and Property. |
| `sortDefinition` | Defines how data is sorted in the visual. |

### 5. Field/Column Reference Structure

Most visuals reference fields using this pattern:

```json
"field": {
  "Column": {
    "Expression": {
      "SourceRef": {
        "Entity": "VW_FIELD_ACTION"
      }
    },
    "Property": "Current Phase"
  }
}
```

or for aggregate fields:

```json
"field": {
  "Aggregation": {
    "Expression": {
      "Column": {
        "Expression": {
          "SourceRef": {
            "Entity": "VW_FIELD_ACTION"
          }
        },
        "Property": "Field Action Number"
      }
    },
    "Function": 5
  }
}
```

## Visual Types in Your Project

Your project includes these visual types:

1. Basic Charts:
   - `barChart`, `columnChart`
   - `lineChart`, `areaChart`
   - `pieChart`, `donutChart`
   - `scatterChart`
   
2. Stacked Variations:
   - `stackedAreaChart`
   - `hundredPercentStackedColumnChart`
   - `hundredPercentStackedBarChart`
   - `hundredPercentStackedAreaChart`
   
3. Combination Charts:
   - `lineStackedColumnComboChart`
   - `lineClusteredColumnComboChart`
   
4. Special Visualizations:
   - `funnel`
   - `gauge`
   - `kpi`
   - `scorecard`
   - `cardVisual`, `card`, `multiRowCard`
   - `waterfallChart`
   - `ribbonChart`
   - `treemap`
   - `decompositionTreeVisual`
   
5. Table Visuals:
   - `tableEx`
   - `pivotTable`
   
6. Map Visuals:
   - `map`
   - `filledMap`
   
7. Filters:
   - `slicer`
   - `advancedSlicerVisual`

## Common Data Structure

All visuals in your project:
1. Use data from the "VW_FIELD_ACTION" entity
2. Most frequently reference:
   - "Current Phase" for categorization
   - "Field Action Type" for series/breakdown
   - Count of "Field Action Number" for values

## Objects Property Examples

The `objects` property contains custom formatting. For example, a filled map with custom colors:

```json
"objects": {
  "dataPoint": [
    {
      "properties": {
        "fillRule": {
          "linearGradient2": {
            "min": { "color": { "expr": { "Literal": { "Value": "'minColor'" } } } },
            "max": { "color": { "expr": { "Literal": { "Value": "'maxColor'" } } } },
            "nullColoringStrategy": { 
              "strategy": { "expr": { "Literal": { "Value": "'asZero'" } } }
            }
          }
        }
      }
    }
  ]
}
```

## Special Visual Configurations

1. **Slicers** have a special `mode` property:
   ```json
   "objects": {
     "data": [
       {
         "properties": {
           "mode": {
             "expr": {
               "Literal": {
                 "Value": "'Basic'"
               }
             }
           }
         }
       }
     ]
   }
   ```

2. **Decomposition Tree** has a property for the number of bars:
   ```json
   "objects": {
     "tree": [
       {
         "properties": {
           "effectiveBarsPerLevel": {
             "expr": {
               "Literal": {
                 "Value": "3L"
               }
             }
           }
         }
       }
     ]
   }
   ```

## Best Practices for Editing visual.json Files

1. Use a schema-aware editor like Visual Studio Code for validation
2. Maintain the schema reference at the top of each file
3. Never change the `name` property as it's referenced by other files
4. Restart Power BI Desktop after manual edits
5. Verify changes in Power BI Desktop before committing to source control

## Visual Naming Convention

Visuals use a 20-character unique identifier as the object name (e.g., "90c2e07d8e84e7d5c026"). One visual in your project uses a more readable name "scorecard". While you can rename these, they must:

1. Consist of only letters, digits, underscores or hyphens
2. Be unique within the report
3. Maintain references - changing names might break internal report references
