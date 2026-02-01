# DrawIO XML Format Reference

This document provides a complete reference for the DrawIO XML file format, enabling programmatic diagram generation.

## File Structure

### Root Element: mxfile

```xml
<?xml version="1.0" encoding="UTF-8"?>
<mxfile host="app.diagrams.net" modified="2026-02-01T00:00:00.000Z" 
        agent="Mozilla/5.0" version="24.0.0" type="device">
  <!-- One or more diagram elements -->
</mxfile>
```

| Attribute | Description |
| --- | --- |
| host | Origin application |
| modified | Last modification timestamp (ISO 8601) |
| agent | User agent string |
| version | DrawIO version |
| type | Storage type: device, browser, google, onedrive, github |

### Diagram Element

```xml
<diagram id="unique-id" name="Page Name">
  <mxGraphModel>
    <!-- Graph model content -->
  </mxGraphModel>
</diagram>
```

Multiple `<diagram>` elements create multi-page diagrams.

### mxGraphModel Element

```xml
<mxGraphModel dx="0" dy="0" grid="1" gridSize="10" guides="1" 
              tooltips="1" connect="1" arrows="1" fold="1" 
              page="1" pageScale="1" pageWidth="827" pageHeight="1169"
              math="0" shadow="0">
  <root>
    <!-- Cells go here -->
  </root>
</mxGraphModel>
```

| Attribute | Description | Default |
| --- | --- | --- |
| dx | Horizontal offset | 0 |
| dy | Vertical offset | 0 |
| grid | Show grid | 1 (true) |
| gridSize | Grid cell size in pixels | 10 |
| guides | Show alignment guides | 1 |
| tooltips | Enable tooltips | 1 |
| connect | Allow connections | 1 |
| arrows | Show connection arrows | 1 |
| fold | Enable folding | 1 |
| page | Show page borders | 1 |
| pageScale | Page scale factor | 1 |
| pageWidth | Page width in pixels | 827 (A4) |
| pageHeight | Page height in pixels | 1169 (A4) |
| math | Enable math typesetting | 0 |
| shadow | Default shadow state | 0 |
| background | Background color | #ffffff |

### Root Container

Every diagram must have exactly two root cells:

```xml
<root>
  <!-- Root cell (always id="0") -->
  <mxCell id="0"/>
  
  <!-- Default layer (always id="1", parent="0") -->
  <mxCell id="1" parent="0"/>
  
  <!-- All other cells are children of "1" -->
</root>
```

## mxCell Element

The mxCell element represents shapes (vertices) and connectors (edges).

### Vertex (Shape)

```xml
<mxCell id="shape-1" value="Label Text" 
        style="rounded=1;whiteSpace=wrap;html=1;fillColor=#dae8fc;strokeColor=#6c8ebf;"
        vertex="1" parent="1">
  <mxGeometry x="100" y="100" width="120" height="60" as="geometry"/>
</mxCell>
```

| Attribute | Description | Required |
| --- | --- | --- |
| id | Unique identifier | Yes |
| value | Label text (supports HTML) | No |
| style | Style string (semicolon-separated) | No |
| vertex | Must be "1" for shapes | Yes |
| parent | Parent cell ID (usually "1") | Yes |
| connectable | Allow connections (default: 1) | No |

### Edge (Connector)

```xml
<mxCell id="edge-1" value="" 
        style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;"
        edge="1" parent="1" source="shape-1" target="shape-2">
  <mxGeometry relative="1" as="geometry"/>
</mxCell>
```

| Attribute | Description | Required |
| --- | --- | --- |
| id | Unique identifier | Yes |
| value | Label text | No |
| style | Style string | No |
| edge | Must be "1" for connectors | Yes |
| parent | Parent cell ID | Yes |
| source | Source vertex ID | Yes (for connections) |
| target | Target vertex ID | Yes (for connections) |

### Geometry

```xml
<!-- Absolute positioning for vertices -->
<mxGeometry x="100" y="100" width="120" height="60" as="geometry"/>

<!-- Relative positioning for edges -->
<mxGeometry relative="1" as="geometry">
  <mxPoint x="160" y="130" as="sourcePoint"/>
  <mxPoint x="280" y="130" as="targetPoint"/>
  <Array as="points">
    <mxPoint x="220" y="130"/>
    <mxPoint x="220" y="200"/>
  </Array>
</mxGeometry>
```

## Style Reference

### Style String Format

Styles are semicolon-separated key=value pairs:

```
rounded=1;whiteSpace=wrap;html=1;fillColor=#dae8fc;strokeColor=#6c8ebf;fontSize=12;
```

### Common Shape Styles

| Property | Values | Description |
| --- | --- | --- |
| rounded | 0, 1 | Rounded corners |
| whiteSpace | wrap, nowrap | Text wrapping |
| html | 0, 1 | Enable HTML in labels |
| fillColor | hex color | Fill color |
| strokeColor | hex color | Border color |
| strokeWidth | number | Border width |
| fontColor | hex color | Text color |
| fontSize | number | Font size |
| fontStyle | 0=normal, 1=bold, 2=italic, 3=bold+italic | Text style |
| align | left, center, right | Horizontal alignment |
| verticalAlign | top, middle, bottom | Vertical alignment |
| opacity | 0-100 | Transparency |
| shadow | 0, 1 | Drop shadow |
| dashed | 0, 1 | Dashed border |
| dashPattern | pattern | e.g., "8 8" |

### Basic Shape Styles

```
# Rectangle
shape=rectangle;rounded=0;whiteSpace=wrap;html=1;

# Rounded Rectangle
rounded=1;whiteSpace=wrap;html=1;

# Ellipse/Circle
ellipse;whiteSpace=wrap;html=1;

# Diamond/Decision
rhombus;whiteSpace=wrap;html=1;

# Parallelogram
shape=parallelogram;perimeter=parallelogramPerimeter;whiteSpace=wrap;html=1;

# Cylinder (Database)
shape=cylinder3;whiteSpace=wrap;html=1;boundedLbl=1;

# Cloud
ellipse;shape=cloud;whiteSpace=wrap;html=1;

# Document
shape=document;whiteSpace=wrap;html=1;boundedLbl=1;

# Hexagon
shape=hexagon;perimeter=hexagonPerimeter2;whiteSpace=wrap;html=1;

# Triangle
triangle;whiteSpace=wrap;html=1;

# Process (Rectangle with double border)
shape=process;whiteSpace=wrap;html=1;backgroundOutline=1;

# Swimlane/Pool
swimlane;fontStyle=1;childLayout=stackLayout;horizontal=1;startSize=30;
```

### Edge Styles

```
# Orthogonal (right-angle connectors)
edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;

# Straight line
edgeStyle=none;rounded=0;html=1;

# Curved
edgeStyle=entityRelationEdgeStyle;rounded=0;html=1;

# Elbow
edgeStyle=elbowEdgeStyle;elbow=horizontal;rounded=0;html=1;

# Isometric
edgeStyle=isometricEdgeStyle;rounded=0;html=1;
```

### Arrow Styles

```
# Start arrow
startArrow=none|classic|block|open|oval|diamond|diamondThin

# End arrow
endArrow=none|classic|block|open|oval|diamond|diamondThin

# Arrow fill
startFill=0|1
endFill=0|1

# Arrow size
startSize=number
endSize=number
```

### Line Styles

```
# Solid (default)
dashed=0;

# Dashed
dashed=1;dashPattern=8 8;

# Dotted
dashed=1;dashPattern=1 4;
```

## Color Palette

### Standard Colors

| Name | Hex | Usage |
| --- | --- | --- |
| Blue Light | #dae8fc | Default fill |
| Blue Dark | #6c8ebf | Default stroke |
| Green Light | #d5e8d4 | Success/OK |
| Green Dark | #82b366 | Success stroke |
| Orange Light | #ffe6cc | Warning |
| Orange Dark | #d79b00 | Warning stroke |
| Red Light | #f8cecc | Error/Danger |
| Red Dark | #b85450 | Error stroke |
| Yellow Light | #fff2cc | Highlight |
| Yellow Dark | #d6b656 | Highlight stroke |
| Purple Light | #e1d5e7 | Special |
| Purple Dark | #9673a6 | Special stroke |
| Gray Light | #f5f5f5 | Neutral |
| Gray Dark | #666666 | Neutral stroke |
| White | #ffffff | Background |
| Black | #000000 | Text |

### Cloud Provider Colors

| Provider | Primary | Secondary |
| --- | --- | --- |
| AWS | #FF9900 | #232F3E |
| Azure | #0089D6 | #68217A |
| GCP | #4285F4 | #EA4335 |

## Complete Example

```xml
<?xml version="1.0" encoding="UTF-8"?>
<mxfile host="app.diagrams.net" modified="2026-02-01T00:00:00.000Z" 
        agent="Copilot" version="24.0.0" type="device">
  <diagram id="simple-flow" name="Simple Flow">
    <mxGraphModel dx="0" dy="0" grid="1" gridSize="10" guides="1" 
                  tooltips="1" connect="1" arrows="1" fold="1" page="1" 
                  pageScale="1" pageWidth="850" pageHeight="1100" 
                  math="0" shadow="0">
      <root>
        <mxCell id="0"/>
        <mxCell id="1" parent="0"/>
        
        <!-- Start -->
        <mxCell id="start" value="Start" 
                style="ellipse;whiteSpace=wrap;html=1;fillColor=#d5e8d4;strokeColor=#82b366;" 
                vertex="1" parent="1">
          <mxGeometry x="200" y="40" width="80" height="40" as="geometry"/>
        </mxCell>
        
        <!-- Process -->
        <mxCell id="process" value="Process Data" 
                style="rounded=1;whiteSpace=wrap;html=1;fillColor=#dae8fc;strokeColor=#6c8ebf;" 
                vertex="1" parent="1">
          <mxGeometry x="180" y="120" width="120" height="60" as="geometry"/>
        </mxCell>
        
        <!-- Decision -->
        <mxCell id="decision" value="Valid?" 
                style="rhombus;whiteSpace=wrap;html=1;fillColor=#fff2cc;strokeColor=#d6b656;" 
                vertex="1" parent="1">
          <mxGeometry x="190" y="220" width="100" height="60" as="geometry"/>
        </mxCell>
        
        <!-- End Success -->
        <mxCell id="end-success" value="Success" 
                style="ellipse;whiteSpace=wrap;html=1;fillColor=#d5e8d4;strokeColor=#82b366;" 
                vertex="1" parent="1">
          <mxGeometry x="120" y="320" width="80" height="40" as="geometry"/>
        </mxCell>
        
        <!-- End Error -->
        <mxCell id="end-error" value="Error" 
                style="ellipse;whiteSpace=wrap;html=1;fillColor=#f8cecc;strokeColor=#b85450;" 
                vertex="1" parent="1">
          <mxGeometry x="280" y="320" width="80" height="40" as="geometry"/>
        </mxCell>
        
        <!-- Connector: Start to Process -->
        <mxCell id="edge1" value="" 
                style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;" 
                edge="1" parent="1" source="start" target="process">
          <mxGeometry relative="1" as="geometry"/>
        </mxCell>
        
        <!-- Connector: Process to Decision -->
        <mxCell id="edge2" value="" 
                style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;" 
                edge="1" parent="1" source="process" target="decision">
          <mxGeometry relative="1" as="geometry"/>
        </mxCell>
        
        <!-- Connector: Decision to Success (Yes) -->
        <mxCell id="edge3" value="Yes" 
                style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;" 
                edge="1" parent="1" source="decision" target="end-success">
          <mxGeometry relative="1" as="geometry"/>
        </mxCell>
        
        <!-- Connector: Decision to Error (No) -->
        <mxCell id="edge4" value="No" 
                style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;" 
                edge="1" parent="1" source="decision" target="end-error">
          <mxGeometry relative="1" as="geometry"/>
        </mxCell>
      </root>
    </mxGraphModel>
  </diagram>
</mxfile>
```

## Groups and Containers

### Group

```xml
<mxCell id="group1" value="Group Label" 
        style="group" 
        vertex="1" connectable="0" parent="1">
  <mxGeometry x="50" y="50" width="200" height="150" as="geometry"/>
</mxCell>

<!-- Child elements reference group as parent -->
<mxCell id="child1" value="Child" 
        style="rounded=1;whiteSpace=wrap;html=1;" 
        vertex="1" parent="group1">
  <mxGeometry x="20" y="20" width="80" height="40" as="geometry"/>
</mxCell>
```

### Container/Swimlane

```xml
<mxCell id="lane1" value="Lane Title" 
        style="swimlane;fontStyle=1;childLayout=stackLayout;horizontal=1;startSize=26;fillColor=#dae8fc;horizontalStack=0;resizeParent=1;resizeParentMax=0;resizeLast=0;collapsible=1;marginBottom=0;strokeColor=#6c8ebf;" 
        vertex="1" parent="1">
  <mxGeometry x="50" y="50" width="200" height="200" as="geometry"/>
</mxCell>
```

## Layers

Additional layers can be created:

```xml
<mxCell id="2" value="Background" parent="0"/>
<mxCell id="3" value="Foreground" parent="0"/>

<!-- Shapes on background layer -->
<mxCell id="bg-shape" ... parent="2"/>

<!-- Shapes on foreground layer -->
<mxCell id="fg-shape" ... parent="3"/>
```

## Special Characters

HTML entities in labels:

| Character | Entity |
| --- | --- |
| < | `&lt;` |
| > | `&gt;` |
| & | `&amp;` |
| " | `&quot;` |
| Newline | `<br>` or `&#xa;` |

## Tips for Programmatic Generation

1. **ID Generation**: Use descriptive IDs like `server-1`, `db-main`, `edge-user-to-api`

2. **Positioning**: Use a grid system (e.g., 10px or 20px increments)

3. **Centering**: Calculate position as: `x = canvas_width/2 - shape_width/2`

4. **Spacing**: Maintain consistent spacing (e.g., 40-60px between shapes)

5. **Validation**: Ensure all source/target IDs in edges exist as vertices

6. **Escaping**: HTML-encode special characters in labels
