# fastapi-jwt-auth-api
from fastapi import FastAPI
from .routes import users, items
from .database import Base, engine

Base.metadata.create_all(bind=engine)

app = FastAPI()

app.include_router(users.router)
app.include_router(items.router)

@app.get("/")
def root():
    return {"message": "FastAPI JWT Auth API"}
from sqlalchemy import Column, Integer, String
from .database import Base

class User(Base):
    __tablename__ = "users"
    id = Column(Integer, primary_key=True, index=True)
    username = Column(String, unique=True, index=True)
    hashed_password = Column(String)
from pydantic import BaseModel

class UserCreate(BaseModel):
    username: str
    password: str

class UserOut(BaseModel):
    id: int
    username: str

    class Config:
        orm_mode = True
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker
from sqlalchemy import create_engine

SQLALCHEMY_DATABASE_URL = "sqlite:///./test.db"

engine = create_engine(SQLALCHEMY_DATABASE_URL, connect_args={"check_same_thread": False})
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)
Base = declarative_base()
from datetime import datetime, timedelta
from jose import JWTError, jwt
from passlib.context import CryptContext

SECRET_KEY = "your_secret_key"
ALGORITHM = "HS256"
ACCESS_TOKEN_EXPIRE_MINUTES = 30

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

def verify_password(plain_pwd, hashed_pwd):
    return pwd_context.verify(plain_pwd, hashed_pwd)

def get_password_hash(password):
    return pwd_context.hash(password)

def create_access_token(data: dict):
    to_encode = data.copy()
    expire = datetime.utcnow() + timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
    to_encode.update({"exp": expire})
    return jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)
from fastapi import APIRouter

router = APIRouter(
    prefix="/users",
    tags=["users"]
)

@router.get("/")
def read_users():
    return [{"username": "user1"}, {"username": "user2"}]
from fastapi import APIRouter

router = APIRouter(
    prefix="/items",
    tags=["items"]
)

@router.get("/")
def read_items():
    return [{"item": "item1"}, {"item": "item2"}]
def test_read_users():
    assert True
