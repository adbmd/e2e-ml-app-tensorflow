# Text classification w/ <img src="https://raw.githubusercontent.com/madewithml/images/master/images/tensorflow.png" width="25rem"> TensorFlow

🚀 This project was created using the Made With ML [boilerplate](https://github.com/madewithml/boilerplate) template. Check it out to start creating your own ML applications.

## Set up
```
virtualenv -p python3.6 venv
source venv/bin/activate
pip install -r requirements.txt
pip install tensorflow==2.1.0
```

## Download embeddings
```bash
python text_classification/utils.py
```

## Training
```bash
python text_classification/train.py \
    --data-url https://raw.githubusercontent.com/madewithml/lessons/master/data/news.csv --lower --shuffle --use-glove
```

## Endpoints
```bash
uvicorn text_classification.app:app --host 0.0.0.0 --port 5000 --reload
GOTO: http://localhost:5000/docs
```

## Inference
### Scripts
```bash
python text_classification/predict.py --text 'The Canadian minister signed in the new federal law.'
```

### cURL
```
curl "http://localhost:5000/predict" \
    -X POST -H "Content-Type: application/json" \
    -d '{
            "inputs":[
                {
                    "text":"The Wimbledon tennis tournament starts next week!"
                },
                {
                    "text":"The Canadian minister signed in the new federal law."
                }
            ]
        }' | json_pp
```

### Requests
```python
import json
import requests

headers = {
    'Content-Type': 'application/json',
}

data = {
    "experiment_id": "latest",
    "inputs": [
        {
            "text": "The Wimbledon tennis tournament starts next week!"
        },
        {
            "text": "The Canadian minister signed in the new federal law."
        }
    ]
}

response = requests.post('http://0.0.0.0:5000/predict',
                         headers=headers, data=json.dumps(data))
results = json.loads(response.text)
print (json.dumps(results, indent=2, sort_keys=False))
```

## Streamlit
```bash
streamlit run text_classification/streamlit.py
GOTO: http://localhost:8501
```

## Tests
```bash
pytest
```

## Docker
1. Build image
```bash
docker build -t text-classification:latest -f Dockerfile .
```
2. Run container
```bash
docker run -d -p 5000:5000 -p 6006:6006 --name text-classification text-classification:latest
```

## Heroku
```
Set `WANDB_API_KEY` as an environment variable.
```

## Directory structure
```
text-classification/
├── datasets/                           - datasets
├── logs/                               - directory of log files
|   ├── errors/                           - error log
|   └── info/                             - info log
├── tests/                              - unit tests
├── text_classification/                - ml scripts
|   ├── app.py                            - app endpoints
|   ├── config.py                         - configuration
|   ├── data.py                           - data processing
|   ├── models.py                         - model architectures
|   ├── predict.py                        - inference script
|   ├── streamlit.py                      - streamlit app
|   ├── train.py                          - training script
|   └── utils.py                          - load embeddings
├── wandb/                              - wandb experiment runs
├── .dockerignore                       - files to ignore on docker
├── .gitignore                          - files to ignore on git
├── CODE_OF_CONDUCT.md                  - code of conduct
├── CODEOWNERS                          - code owner assignments
├── CONTRIBUTING.md                     - contributing guidelines
├── Dockerfile                          - dockerfile to containerize app
├── LICENSE                             - license description
├── logging.json                        - logger configuration
├── Procfile                            - process script for Heroku
├── README.md                           - this README
├── requirements.txt                    - requirementss
├── setup.sh                            - streamlit setup for Heroku
└── sweeps.yaml                         - hyperparameter wandb sweeps config
```

## Overfit to small subset
```
python text_classification/train.py \
    --data-url https://raw.githubusercontent.com/madewithml/lessons/master/data/news.csv --lower --shuffle --data-size 0.1 --num-epochs 3
```

## Experiments
1. Random, unfrozen, embeddings
```
python text_classification/train.py \
    --data-url https://raw.githubusercontent.com/madewithml/lessons/master/data/news.csv --lower --shuffle
```
2. GloVe, frozen, embeddings
```
python text_classification/train.py \
    --data-url https://raw.githubusercontent.com/madewithml/lessons/master/data/news.csv --lower --shuffle --use-glove --freeze-embeddings
```
3. GloVe, unfrozen, embeddings
```
python text_classification/train.py \
    --data-url https://raw.githubusercontent.com/madewithml/lessons/master/data/news.csv --lower --shuffle --use-glove
```

## Helpful docker commands
• Build image
```
docker build -t madewithml:latest -f Dockerfile .
```

• Run container if using `CMD ["python", "app.py"]` or `ENTRYPOINT [ "/bin/sh", "entrypoint.sh"]`
```
docker run -p 5000:5000 --name madewithml madewithml:latest
```

• Get inside container if using `CMD ["/bin/bash"]`
```
docker run -p 5000:5000 -it madewithml /bin/bash
```

• Run container with mounted volume
```
docker run -p 5000:5000 -v $PWD:/root/madewithml/ --name madewithml madewithml:latest
```

• Other flags
```
-d: detached
-ti: interative terminal
```

• Clean up
```
docker stop $(docker ps -a -q)     # stop all containers
docker rm $(docker ps -a -q)       # remove all containers
docker rmi $(docker images -a -q)  # remove all images
```
