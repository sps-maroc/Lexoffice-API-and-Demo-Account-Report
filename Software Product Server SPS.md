# Software Product Server SPS

<img src="./logo.png" alt="SPS Logo" width="200"/>
---

# Lexoffice API and Demo Account Report

## Introduction to Lexoffice

Lexoffice is a cloud-based accounting software primarily aimed at small businesses, freelancers, and self-employed individuals in Germany. It offers a range of functionalities including invoicing, expense tracking, bank reconciliation, and financial reporting. The software is designed to simplify accounting tasks and ensure compliance with German tax regulations.

## Lexoffice API Overview

The Lexoffice API is a RESTful API that allows developers to integrate their applications with Lexoffice. This enables programmatic access to push and pull data, such as contacts, invoices, vouchers, and other accounting-related information. The API uses JSON for data exchange and requires an API key for authentication.

**Key Features of the API:**

*   **Authentication:** Access to the API is secured using an API key (access token) which users can generate from their Lexoffice account settings (specifically at `https://app.lexoffice.de/addons/public-api`). This key must be included in the `Authorization` header of API requests as a Bearer token.
*   **Rate Limits:** To ensure fair usage and stability, the Lexoffice API implements rate limits. Currently, clients can make up to 2 requests per second. Exceeding this limit will result in an HTTP 429 (Too Many Requests) error.
*   **Endpoints:** The API provides various endpoints to interact with different resources within Lexoffice. These include:
    *   Contacts
    *   Articles (Products/Services)
    *   Invoices
    *   Credit Notes
    *   Quotations
    *   Delivery Notes
    *   Vouchers (for expenses)
    *   Files (for attaching documents to vouchers, etc.)
    *   Profile (to get organization details)

## Accessing Invoices and General Data via API

The Lexoffice API offers comprehensive capabilities for managing and retrieving invoices and other general account data.

**Retrieving Invoices:**

*   **Endpoint:** `GET /v1/invoices` (to list invoices, with pagination and filtering options) and `GET /v1/invoices/{invoiceId}` (to retrieve a specific invoice).
*   **Invoice Data:** The API returns detailed information about invoices, including customer details, line items, amounts, tax information, and status.
*   **Downloading Invoice PDFs:** To download an invoice as a PDF, you would typically retrieve the invoice details first. The API documentation specifies that created documents (like invoices) can be rendered as PDF files. The `GET /v1/invoices/{invoiceId}/document` endpoint is used to retrieve the invoice document. The response will contain the file ID if the document is available. You can then use the Files API (`GET /v1/files/{fileId}`) to download the actual PDF file. The `Accept` header should be set to `application/pdf` when requesting the file content if a direct PDF download endpoint is available for the invoice document itself, or you retrieve the file ID and then download it via the files endpoint.

**Retrieving General Account Data:**

Lexoffice provides various endpoints to access different types of data:

*   **Contacts:** `GET /v1/contacts` and `GET /v1/contacts/{contactId}` for customer and supplier information.
*   **Vouchers (Expenses/Receipts):** `GET /v1/voucherlist` (to list vouchers) and `GET /v1/vouchers/{voucherId}` (for specific voucher details). This is crucial for a complete financial overview.
*   **Articles (Products/Services):** `GET /v1/articles` and `GET /v1/articles/{articleId}`.
*   **Profile:** `GET /v1/profile` to fetch details about the Lexoffice account/organization itself.

Data is generally retrieved using GET requests to the respective endpoints, and responses are in JSON format.

## Using the Lexoffice API with Python

Interacting with the Lexoffice REST API using Python is straightforward. You can use standard libraries like `requests` to make HTTP requests.

**Example (Conceptual Python Snippet for retrieving invoices):**

```python
import requests
import json

API_KEY = "YOUR_LEXOFFICE_API_KEY"
BASE_URL = "https://api.lexoffice.io/v1"

headers = {
    "Authorization": f"Bearer {API_KEY}",
    "Accept": "application/json"
}

# Example: Get a list of invoices
response = requests.get(f"{BASE_URL}/invoices", headers=headers)

if response.status_code == 200:
    invoices_data = response.json()
    # Process your invoices_data here
    print(json.dumps(invoices_data, indent=4))
    
    # Example: Get a specific invoice (assuming you have an invoiceId)
    # if invoices_data.get("content"):
    #     invoice_id = invoices_data["content"][0]["id"]
    #     invoice_response = requests.get(f"{BASE_URL}/invoices/{invoice_id}", headers=headers)
    #     if invoice_response.status_code == 200:
    #         specific_invoice = invoice_response.json()
    #         print(json.dumps(specific_invoice, indent=4))
    #
    #         # Example: Download the invoice PDF
    #         # First, get the document metadata which might contain a fileId
    #         document_meta_response = requests.get(f"{BASE_URL}/invoices/{invoice_id}/document", headers=headers)
    #         if document_meta_response.status_code == 200:
    #             document_meta = document_meta_response.json()
    #             if document_meta.get("documentFileId"):
    #                 file_id = document_meta["documentFileId"]
    #                 pdf_headers = {
    #                     "Authorization": f"Bearer {API_KEY}",
    #                     "Accept": "application/octet-stream" # Or application/pdf depending on API specifics
    #                 }
    #                 pdf_response = requests.get(f"{BASE_URL}/files/{file_id}", headers=pdf_headers)
    #                 if pdf_response.status_code == 200:
    #                     with open(f"invoice_{invoice_id}.pdf", "wb") as f:
    #                         f.write(pdf_response.content)
    #                     print(f"Invoice {invoice_id}.pdf downloaded.")
    #                 else:
    #                     print(f"Failed to download PDF: {pdf_response.status_code}")    
    #             else:
    #                 print("No documentFileId found for the invoice.")
    #         else:
    #             print(f"Failed to get invoice document metadata: {document_meta_response.status_code}")
    #     else:
    #         print(f"Failed to retrieve specific invoice: {invoice_response.status_code}")
elif response.status_code == 401:
    print("Unauthorized. Check your API Key.")
elif response.status_code == 429:
    print("Rate limit exceeded. Please try again later.")
else:
    print(f"Failed to retrieve invoices: {response.status_code} - {response.text}")

```

**Note:** The exact field names and flow for PDF download (e.g., `documentFileId`) should be verified against the latest API documentation details for the `/invoices/{invoiceId}/document` endpoint, as the provided snippet is conceptual based on common REST API patterns for file downloads.

## Creating and Using a Demo Account for Development

Lexoffice offers a free trial that can be used as a demo account for development and testing purposes. This allows you to explore the software's features and interact with the API using test data without any financial commitment.

**How to Create a Demo/Trial Account:**

1.  **Navigate to the Trial Registration Page:** You can typically find the trial registration page on the official Lexoffice website. A direct link that was found is `https://www.lexoffice.de/test/`, which may redirect to a registration form like `https://app.lexoffice.de/signup/app/trial`.
2.  **Register for the Free Trial:** The registration process usually requires providing an email address and setting up a password. Lexoffice states that the trial does not require bank details or a credit card, and it ends automatically without converting into a paid subscription unless you actively choose to subscribe.
3.  **Access Your Trial Account:** Once registered, you can log in to your Lexoffice trial account.

**Using the Demo Account for API Development:**

1.  **Generate an API Key:** Within your trial account, navigate to the API settings (usually under Add-ons or a similar section, specifically `https://app.lexoffice.de/addons/public-api` as per the documentation) to generate your API key.
2.  **Populate with Test Data:** You can manually create sample invoices, contacts, expenses, etc., within your trial account. This data will then be accessible via the API, allowing you to test your integration thoroughly.
3.  **API Interaction:** Use the generated API key in your Python scripts (or other applications) to make calls to the Lexoffice API endpoints as described above. You can test retrieving, creating (if supported by the endpoint and your use case), and updating data.

**Capabilities and Limitations of the Demo Account:**

*   **Full Functionality (Typically):** Trial accounts usually provide access to most, if not all, features of the paid Lexoffice versions, including API access. This is ideal for comprehensive development and testing.
*   **Time-Limited:** The trial period is for a limited duration. After it expires, you will likely lose access unless you subscribe to a paid plan.
*   **Test Data Only:** It is crucial to use only test or dummy data in your demo account, especially if you are testing write operations (creating/updating data).

## Conclusion

The Lexoffice API provides a robust way to integrate with its accounting platform, offering access to a wide range of data including invoices. By utilizing Python and the free trial account for development, you can effectively build and test applications that leverage Lexoffice's capabilities.

## References

*   Lexoffice API Documentation: [https://developers.lexoffice.io/](https://developers.lexoffice.io/)
*   Lexoffice Trial Registration (example link): [https://www.lexoffice.de/test/](https://www.lexoffice.de/test/)

This report provides a comprehensive overview based on the available documentation. Always refer to the official Lexoffice developer portal for the most current and detailed information.
