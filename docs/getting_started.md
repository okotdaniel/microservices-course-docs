## Introduction

This application will be a fully-fledged PDF converter, enabling users to convert various documents into PDF format. It operates on a diverse technology stack, which will be detailed below.
First, let's explore the stack that powers this application and the rationale behind our choices.
We have:
MongoDB and PostgreSQL as our database providers.
Django and FastAPI to power the different services within the application.
Next.js on the frontend, facilitating the upload of documents and the retrieval of converted PDFs.
RabbitMQ as a notification service and runtime broker.

## Why We Chose This Stack

At first glance, one might question the need for such a complex configuration for a seemingly simple task like PDF conversion. This is a valid point. However, the answer lies in the benefits of scalability, flexibility, and maintainability that this stack provides.
Using a combination of Django and FastAPI allows us to leverage the strengths of both frameworks: Django for its robust features and FastAPI for its speed and efficiency in handling asynchronous requests. The choice of MongoDB and PostgreSQL offers us the flexibility to manage different types of data effectively.
Moreover, Next.js enhances the user experience by providing a seamless interface for document uploads and conversions. Finally, RabbitMQ ensures that our application can handle notifications and background tasks efficiently, making it more responsive and reliable.
In summary, while the core functionality may seem straightforward, the chosen stack supports a scalable and efficient application that can grow and adapt to future needs.