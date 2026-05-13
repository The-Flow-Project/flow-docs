# Services

The Flow environment is build on a modular microservice architecture. For every HTR/ATR task, there are independent services. They are built with FastAPI.  
We are in *beta*-status, but we are working hard on them.  

## Tasks

- **Preprocessing** - Prepare TrOCR training or inference material from XML files.  
- **Inference** - Perform inference (text prediction with trained TrOCR models) and evaluation on pre-processed images.
- **Training** - Train a TrOCR model with your pre-processed ground-truth data.  
