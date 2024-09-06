

# FROM ubuntu:latest
# RUN apt-get update
# RUN apt-get install python3-pip -y
# RUN apt-get install mysql-server -y
# RUN apt-get install libmysqlclient-dev -y
# RUN mkdir /code
# WORKDIR /code
# ADD requirements.txt /code/
# RUN pip3 install -r requirements.txt
# ADD . /code/
# CMD ["python3", "backend.py" ]


FROM ubuntu:latest

# Install dependencies
RUN apt-get update && \
    apt-get install -y python3-pip python3-venv mysql-server libmysqlclient-dev

# Create and set working directory
RUN mkdir /code
WORKDIR /code

# Copy the requirements file and create a virtual environment
COPY requirements.txt /code/
RUN python3 -m venv venv

# Install dependencies inside the virtual environment
RUN /code/venv/bin/pip install --upgrade pip && \
    /code/venv/bin/pip install -r requirements.txt

# Copy the rest of the application code
COPY . /code/

# Command to run the application
CMD ["/code/venv/bin/python3", "backend.py"]
