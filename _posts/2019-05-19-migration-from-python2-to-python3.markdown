---
layout: post
title:  "Migration from Python2 to Python3"
date:   2019-05-19 07:00:00 +0700
tags: python django
---
A month ago, due to incident with old software, we have made it a schedule to update our software to update to the latest stable version. Over the last few weeks, I have had a chance to work on the migration of Python2 to Python3 for multiple services. There were some obstacles on the way, but thanks to our well-written testcase (and Stackoverflow), I have managed to migrate most of our services to Python3. Please note that some parts of the guide includes not only Python, but some others part belongs to our stack, and process

#### Prerequisite
Research about the best Python3 version you can get. For now, Python does not have the definition of LTS yet, so my recommendation would be choosing the latest one that are fit to all of your libraries. In our case, we find that Python3.7 actually have most problem with our current stack, so we go with 3.6 instead
#### Conversaion
- Run [2to3](https://docs.python.org/2/library/2to3.html) to have a quick code conversaion, I would normally use
`2to3 -W -n .` to let it scan through the whole project directory. With a large codebase, I wonder how would we possibly modify all the files with this help
- Modify Dockerfile and requirements.txt, to update Python and Django version
- Building the Docker image, normally, some of the library incompabatible problem will surface here, so, a few rebuild may needed
- After that, running `python manage.py shell`, and import model(, serializers) files, will problem display some code change required for Django also
- Lastly, run `python manage.py makemigrations`, possibly you would need to make change to some past migration files. The reason is that in the default string value b'some-string' would need to change to 'some-string
- Now, if you have test cases prepared, prepare to run them to see if things still work normally

#### Common issues
- CSV and file reading/writing: With Python2, the default mode would be binary, while with Python3, the default would be text mode. Thus, some file reading/writing code from Python2 -> Python3 may break. What I would advise is
  - See if your file reading code would be using text mode or not, if yes, use that. For some cases, it would be better to use the binary mode in Python3. In our case, when a user upload a file, it would be in binary, if we want it to seamlessly go through our CSV Helper, we have to convert it to string. However, this would create two piece of data, which is very inefficient. In our case, we added some check and modification, so that whichever type of data passed in can pass through CSV Helper
  - Check if you are currently encoding the output to bytes before writing it to file, if yes, remove that. With the same reason above, Python would write file in string modes

- Exception would not have message: In Python2, to get the exception message, we would use `exception.message`, we would have to change to `str(exception)`. Otherwise, you would get another Exception, since `Exception` instance does not have `message` variable

- Using subprocess: if we are using subprocess to connect to spawn new processes, and connect to their pipes, we would have made sure the stdout is decode to string in Python3, so that other string manipulation can take place as normal

