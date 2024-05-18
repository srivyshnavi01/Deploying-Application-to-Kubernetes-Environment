# Stage 1: Build stage
FROM python:3 AS builder

# Set the working directory inside the container
WORKDIR /app

# Copy the requirements file into the container at /app
COPY requirements.txt .

# Install dependencies
RUN pip install --no-cache-dir virtualenv
RUN virtualenv venv
RUN /bin/bash -c "source venv/bin/activate && pip install --no-cache-dir -r requirements.txt"

# Copy the application code into the container at /app
COPY smileapp /app

# Stage 2: Final stage
FROM python:3-slim

# Set the working directory inside the container
WORKDIR /app

# Copy the application code from the build stage
COPY --from=builder /app .

# Expose port 5000 to the outside world
EXPOSE 8000

# Set the entry point and command to run the Flask application
ENTRYPOINT ["venv/bin/python"]
CMD ["app.py", "runserver", "0.0.0.0:8000"]
