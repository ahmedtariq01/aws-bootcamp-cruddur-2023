FROM python:3.10-slim-buster

# Inside container
# Create a directory for the app
WORKDIR /backend-flask

# Outside container -> Inside container
# libraries required for the app
COPY requirements.txt requirements.txt

# Inside container
# Install the python libraries used for the app
RUN pip3 install -r requirements.txt

# Outside container -> Inside container
# . means everything in the current directory
# . . means everything in the parent directory
COPY . .

ENV FLASK_ENV=development

EXPOSE ${PORT}
CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0", "--port=4567"]