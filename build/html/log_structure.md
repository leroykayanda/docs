Log Structure in recon-backend
==============================

We are only logging from the handlers by choice to keep the logs clean and concise. All handlers should log the following using `structlog`. **Note**: Be cautious when logging requests and responses to avoid logging sensitive data.

Log Prefix
----------

The log prefix that should be used in all logs is:

.. code-block:: python

   log_prefix = f"[{handler_name}]APIView: "

Using structlog for Logging
---------------------------

We are using `structlog` to capture structured logs, making them more readable and machine-parsable. Here's how you can log requests and responses, while ensuring **PII and sensitive data are not logged**.

.. code-block:: python

   import structlog

   logger = structlog.get_logger()

   def log_request(request):
       log_prefix = f"[{handler_name}]APIView: "
       # Log non-sensitive parts of the request
       sanitized_data = sanitize_request_data(request.data)
       logger.info(f"{log_prefix}Request", data=sanitized_data)

   def log_response(response):
       log_prefix = f"[{handler_name}]APIView: "
       # Log non-sensitive parts of the response
       sanitized_data = sanitize_response_data(response.data)
       logger.info(f"{log_prefix}Response", data=sanitized_data)

   def sanitize_request_data(data):
       # Logic to remove or mask PII from the request data
       return {key: value for key, value in data.items() if key not in ['password', 'ssn', 'credit_card']}

Logging Example with structlog
------------------------------

Below is an example that ensures no sensitive information is logged:

.. code-block:: python

   import structlog

   logger = structlog.get_logger()

   def handler_function(request):
       log_prefix = f"[HandlerName]APIView: "
       sanitized_data = sanitize_request_data(request.data)
       logger.info(f"{log_prefix}Request", data=sanitized_data)

       # Process the request
       response = process_request(request)

       sanitized_response = sanitize_response_data(response.data)
       logger.info(f"{log_prefix}Response", data=sanitized_response)
       return response

Logs in Scalyr
--------------

All logs are pushed to **Scalyr**. By following the structured logging format outlined above, filtering and searching logs in Scalyr becomes significantly easier, while also ensuring that **sensitive data** is not exposed in logs.

PII Handling and Data Sanitization
----------------------------------

When logging request and response data, it's crucial to ensure that **PII (Personally Identifiable Information)** and other sensitive data (such as passwords, credit card numbers, social security numbers) are not logged. The example above uses a `sanitize_request_data` function to remove or mask sensitive fields.

Benefits of structlog and Scalyr
--------------------------------

- **Structured logs**: Logs are output in key-value pairs, making them easier to parse by log management systems like Scalyr.
- **Consistency**: Every log entry follows the same format with the `log_prefix` for better readability.
- **PII and sensitive data protection**: Logging sensitive fields such as passwords, social security numbers, or credit card data should be avoided by using sanitization functions.
- **Enhanced filtering**: Since the logs are structured, filtering specific fields in Scalyr becomes simpler, enabling more efficient log searches and analysis.
- **Centralized logs**: All logs from the application are pushed to Scalyr, providing a single source for monitoring, troubleshooting, and auditing.

Following this approach ensures that logs are not only consistent and structured but also compliant with privacy standards by avoiding logging PII and sensitive data.