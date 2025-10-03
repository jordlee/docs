# Kando System Diagrams

This file contains JSON prompts for generating diagrams using nanobanana (or similar diagramming tools).

## Diagram 1: User Experience Flow

**Purpose**: Visualize the overall process of using Kando from a user's perspective

```json
{
  "title": "How Kando Works - User Experience",
  "description": "Simple 3-step process showing how developers use Kando to get SDK answers",
  "style": "modern, clean, friendly",
  "color_scheme": "orange (#FF8C00) primary, with gradients to light orange (#FFA07A)",
  "diagram_type": "horizontal flow",
  "steps": [
    {
      "number": 1,
      "title": "You Ask a Question",
      "icon": "speech bubble or chat icon",
      "examples": [
        "How do I connect to a camera?",
        "Show me how to capture an image in C#"
      ],
      "visual": "Developer at computer with AI assistant (Claude/ChatGPT) interface"
    },
    {
      "number": 2,
      "title": "Kando Searches for Answers",
      "icon": "magnifying glass or search icon",
      "details": [
        "8,962+ pieces of content",
        "Guides, API references, code examples",
        "SDK V1.14.00 and V2.00.00"
      ],
      "visual": "Kando logo/icon with search animation, database in background"
    },
    {
      "number": 3,
      "title": "You Get Instant Results",
      "icon": "lightbulb or checkmark icon",
      "output": [
        "Documentation",
        "Function details",
        "Working code examples"
      ],
      "visual": "AI assistant showing formatted results with code snippets"
    }
  ],
  "arrows": "smooth, flowing arrows between steps with subtle animation feel",
  "annotations": [
    "All happens in your AI conversation - no need to leave"
  ]
}
```

---

## Diagram 2: Technical Architecture (Simplified)

**Purpose**: Behind-the-scenes view of the RAG/MCP system architecture

```json
{
  "title": "Kando Technical Architecture",
  "description": "Simplified system architecture showing how Kando processes queries and returns results",
  "style": "modern, technical but approachable, clean lines",
  "color_scheme": "orange (#FF8C00) for main components, gray (#333) for supporting elements, light orange (#FFA07A) for highlights",
  "diagram_type": "layered architecture / data flow",
  "components": [
    {
      "layer": "User Interface",
      "position": "top",
      "elements": [
        {
          "name": "Developer Query",
          "description": "Plain English question",
          "icon": "message/chat bubble"
        }
      ]
    },
    {
      "layer": "AI Layer",
      "position": "top-middle",
      "elements": [
        {
          "name": "AI Assistant",
          "options": ["Claude", "ChatGPT"],
          "icon": "robot/AI brain",
          "color": "orange accent"
        }
      ]
    },
    {
      "layer": "Integration Layer",
      "position": "middle",
      "elements": [
        {
          "name": "MCP Server",
          "description": "Model Context Protocol",
          "location": "Railway Cloud",
          "icon": "server/cloud",
          "color": "primary orange"
        }
      ]
    },
    {
      "layer": "Processing Layer",
      "position": "middle-bottom",
      "elements": [
        {
          "name": "RAG Search Engine",
          "description": "Retrieval-Augmented Generation",
          "icon": "gears/processor",
          "color": "orange"
        },
        {
          "name": "AI Intent Mapping",
          "description": "Understands query context",
          "icon": "brain/network",
          "color": "light orange"
        }
      ]
    },
    {
      "layer": "Data Layer",
      "position": "bottom",
      "elements": [
        {
          "name": "Vector Database",
          "storage": "Pinecone Cloud",
          "content": "8,962+ SDK Chunks",
          "icon": "database/storage",
          "color": "dark gray"
        }
      ]
    }
  ],
  "data_flow": [
    {
      "from": "Developer Query",
      "to": "AI Assistant",
      "label": "Question",
      "style": "solid arrow"
    },
    {
      "from": "AI Assistant",
      "to": "MCP Server",
      "label": "MCP Request",
      "style": "solid arrow"
    },
    {
      "from": "MCP Server",
      "to": "RAG Search Engine",
      "label": "Search Query",
      "style": "solid arrow"
    },
    {
      "from": "RAG Search Engine",
      "to": "AI Intent Mapping",
      "label": "Query Expansion",
      "style": "dashed arrow (parallel process)"
    },
    {
      "from": "RAG Search Engine",
      "to": "Vector Database",
      "label": "Semantic Search",
      "style": "solid arrow"
    },
    {
      "from": "Vector Database",
      "to": "RAG Search Engine",
      "label": "Relevant Content",
      "style": "solid arrow (return)"
    },
    {
      "from": "RAG Search Engine",
      "to": "MCP Server",
      "label": "Results",
      "style": "solid arrow (return)"
    },
    {
      "from": "MCP Server",
      "to": "AI Assistant",
      "label": "Formatted Data",
      "style": "solid arrow (return)"
    },
    {
      "from": "AI Assistant",
      "to": "Developer Query",
      "label": "Answer + Code",
      "style": "solid arrow (return)"
    }
  ],
  "callouts": [
    {
      "component": "Vector Database",
      "text": "📖 Documentation\n🔧 API References\n💻 Code Examples\n📊 Compatibility Tables",
      "position": "right side"
    },
    {
      "component": "MCP Server",
      "text": "13+ Specialized Search Tools",
      "position": "left side"
    },
    {
      "component": "AI Intent Mapping",
      "text": "Understands intent even without exact API names",
      "position": "right side"
    }
  ],
  "visual_style": {
    "boxes": "rounded corners, subtle shadows",
    "arrows": "smooth curves, not rigid straight lines",
    "icons": "simple, modern, minimalist",
    "fonts": "clean sans-serif, readable hierarchy"
  }
}
```

---

## Usage Instructions

### For nanobanana or similar tools:

1. Copy the JSON prompt for the diagram you want to create
2. Paste it into your diagramming tool's prompt/input field
3. Adjust colors, spacing, or styling as needed
4. Export as SVG or PNG for use in documentation

### Style Guidelines

- **Colors**: Use Sony Developer Platform brand colors (Orange #FF8C00, Light Orange #FFA07A, Dark Orange #CD6600)
- **Icons**: Simple, modern, not overly detailed
- **Text**: Keep concise, matching the simplified language from the Overview page
- **Layout**: Clean, spacious, easy to follow left-to-right or top-to-bottom

### Recommended Dimensions

- **User Experience Flow**: 1200px wide x 400px tall (horizontal)
- **Technical Architecture**: 800px wide x 1000px tall (vertical/layered)
