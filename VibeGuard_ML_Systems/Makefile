PYTHON ?= python
DATA ?= data/motor_vibration.csv
ARTIFACTS ?= artifacts
CONFIG ?= configs/default.json

.PHONY: setup data train validate evaluate infer benchmark optimize test lint format-check demo api docker

setup:
	$(PYTHON) -m pip install -e ".[dev]"

data:
	vibeguard generate-data --output $(DATA) --samples-per-class 1000 --seed 42

train: data
	vibeguard train --data $(DATA) --config $(CONFIG) --output-dir $(ARTIFACTS)

validate:
	vibeguard validate --data $(DATA) --checkpoint $(ARTIFACTS)/model.pt

evaluate:
	vibeguard evaluate --data $(DATA) --checkpoint $(ARTIFACTS)/model.pt

infer:
	vibeguard predict-window --input examples/sample_vibration_window.json --checkpoint $(ARTIFACTS)/model.pt

benchmark:
	vibeguard benchmark --checkpoint $(ARTIFACTS)/model.pt

optimize:
	vibeguard optimize --checkpoint $(ARTIFACTS)/model.pt --data $(DATA) --output-dir $(ARTIFACTS)/optimization

test:
	pytest -q

lint:
	ruff check .

format-check:
	ruff format --check .

demo: train validate evaluate infer optimize

api:
	uvicorn vibeguard.service:app --host 127.0.0.1 --port 8000 --reload

docker:
	docker compose up --build
