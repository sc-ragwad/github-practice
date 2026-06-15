# Pydantic Learning Documentation

### Topics Covered:
- Creating Structured Data
- Data Validation
- Dict, JSON Serialization and Deserialization
- Response and Request Validation
- Aliases and Enums
---
Many of the method names have been deprecated in pydantic v2, so names of the methods used are updated as per the terminal suggestions

---
### Creating Structured Data
We can define a model of our data and if all the fields are not present as per the model, we get an error

```py
from pydantic import BaseModel
from fastapi import FastAPI

app = FastAPI()

class User(BaseModel):
    name:str
    email: str
    id: int

# user = User(name = "Raghav", email = "raghav@iiit") # throws an error: id not found
user = User(name = "Raghav", email = "raghav@iiit", id = 1) # no error

print(user)
```
# Data Validation
We can correctly validate if our data (float, int, NameEmail, Email etc.) is a valid input according to that field by importing the types and using them as the type while defining our model

```py
# pip install "fastapi[standard]==0.136.3" "pydantic[email]==2.13.4

from pydantic import BaseModel, EmailStr
from fastapi import FastAPI

app = FastAPI()

class User(BaseModel):
    name:str
    email: EmailStr
    id: int

# user = User(name = "Raghav", email = "raghav@iiit", id = 1) #   throws an error: email not valid (does not have a '.' after '@')

user = User(name = "Raghav", email = "raghav@iiitd.ac.in", id = 1) # no error

print(user)
```

We can even create our own validators.

```py
from pydantic import BaseModel, EmailStr, validator
from fastapi import FastAPI

app = FastAPI()

class User(BaseModel):
    name:str
    email: EmailStr
    id: int

    @validator("id")
    def validate_id(cls, value):
        if value <= 0:
            raise ValueError(f'ID must be a positive number {value}')
        return value

# user = User(name = "Raghav", email = "raghav@iiitd.ac.in", id = 0) # error: id must be positive
user = User(name = "Raghav", email = "raghav@iiitd.ac.in", id = 1) # no error

print(user)
```
---
### Dict, JSON Serialization and Deserialization

```py
from pydantic import BaseModel, EmailStr, field_validator
from fastapi import FastAPI

app = FastAPI()

class User(BaseModel):
    name:str
    email: EmailStr
    id: int

    @field_validator("id")
    @classmethod
    def validate_id(cls, value):
        if value <= 0:
            raise ValueError(f'ID must be a positive number {value}')
        return value

user = User(name = "Raghav", email = "raghav@iiitd.ac.in", id = 1)

#dictionary
user_to_dict = user.model_dump()
print(user_to_dict)

#serialization
user_to_json = user.model_dump_json()
print(user_to_json)

#deserialization
json_to_user = User.model_validate_json(user_to_json)
print(json_to_user)
```
---

### Response and Request Validation

```py
from pydantic import BaseModel, EmailStr, field_validator
from fastapi import FastAPI

app = FastAPI()

class User(BaseModel):
    name: str
    email: EmailStr
    id: int
    password: str 

    @field_validator("id")
    @classmethod
    def validate_id(cls, value):
        if value <= 0:
            raise ValueError(f'ID must be a positive number {value}')
        return value

class UserResponse(BaseModel): #response model for password
    name: str
    email: EmailStr
    id: int

@app.post("/users", response_model=UserResponse)
def create_user(user: User):
    return user
```

![alt text](image-1.png)
```txt
using id 0 is giving error as expected
```

![alt text](image.png)
```txt
using all the correct input fields gives the correct output with password stripped out from the response
```

---

### Aliases and Enums

```py
from pydantic import BaseModel, EmailStr, field_validator, Field, ConfigDict
from fastapi import FastAPI
from enum import Enum

app = FastAPI()

class Role(str, Enum): 
    ADMIN = "admin"
    USER = "user"

class User(BaseModel):
    name: str = Field(alias="Full Name") # alias 
    email: EmailStr
    id: int
    role: Role = Role.USER # default = user. only accepts admin or user

    model_config = ConfigDict(populate_by_name=True) # allows us to use our python variable name instead of just alias

    @field_validator("id")
    @classmethod
    def validate_id(cls, value):
        if value <= 0:
            raise ValueError(f'ID must be a positive number {value}')
        return value

@app.post("/users")
def create_user(user: User):
    return {"message": f"Created {user.role.value} named {user.name}"} # we use user.name because fullname was just alias
```

![alt text](image-4.png)
```txt
alias is working, using abc as role throws error
```

![alt text](image-3.png)

```txt
using admin works
```
---