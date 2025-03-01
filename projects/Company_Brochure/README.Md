#Company_Brochure

## Overview
This project demonstrates the Multi-shot technique for guiding LLM behavior through examples and specific instructions. By leveraging an LLM's ability to understand context and make relevance decisions, we can simplify web scraping tasks that would otherwise require complex coding.

## Key Techniques
- **Multi-shot learning**: Providing multiple examples to teach the model how to respond
- **Format specification**: Requesting responses in specific formats like JSON or Markdown
- **Relevance filtering**: Using the LLM to determine what content is important

## Benefits
The main advantage of using LLMs for web scraping is their ability to intelligently decide what content is relevant for a brochure, regardless of website structure. This approach eliminates the need for custom parsing logic for each site.

## Implementation Process

### Step 1: Collect URLs
Gather all URLs from a target website:
- Use the `requests` library to fetch HTML content
- Parse the HTML with `BeautifulSoup` to extract links

### Step 2: Filter Relevant Links
Use an LLM to identify important links:
- Implement Multi-shot prompting with clear examples
- Request JSON response format with a dictionary of links
- Each link entry should include "type" and "url" information

### Step 3: Generate Brochure
Create the final brochure using the pre-filtered links:
- Pass the LLM-selected links to a new prompt
- Instruct the LLM to extract and organize content from these links
- Request the response in Markdown format for easy rendering

## Conclusion
This approach demonstrates how LLMs can streamline web scraping workflows by handling complex decision-making that would traditionally require extensive custom code.