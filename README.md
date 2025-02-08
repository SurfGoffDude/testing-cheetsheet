# Шпаргалка по тестированию кода Django

## 1. Установка необходимых пакетов

Для тестирования в Django обычно используется встроенный модуль `unittest`, но также можно использовать `pytest` для более гибкого тестирования.

```bash
pip install pytest pytest-django
```

## 2. Структура тестов

Тесты в Django обычно размещаются в файле внутри приложения или в директории `tests/` с несколькими файлами.

```
myapp/
    tests/
        __init__.py
        test_models.py
        test_views.py
        test_forms.py
    models.py
    views.py
```

## 3. Написание тестов

### 3.1. Тестирование models

Пример теста:

```python
from django.test import TestCase
from myapp.models import MyModel

class MyModelTest(TestCase):
    def setUp(self):
        MyModel.objects.create(name="Test Model", description="This is a test model")

    def test_model_creation(self):
        model = MyModel.objects.get(name="Test Model")
        self.assertEqual(model.description, "This is a test model")
```

### 3.2. Тестирование views

Пример теста для view:

```python
from django.test import TestCase, Client
from django.urls import reverse
from myapp.models import MyModel

class MyViewTest(TestCase):
    def setUp(self):
        self.client = Client()
        MyModel.objects.create(name="Test Model", description="This is a test model")

    def test_view_url_exists_at_desired_location(self):
        response = self.client.get('/my-view-url/')
        self.assertEqual(response.status_code, 200)

    def test_view_uses_correct_template(self):
        response = self.client.get(reverse('my-view-name'))
        self.assertEqual(response.status_code, 200)
        self.assertTemplateUsed(response, 'my_template.html')
```

## 4. Запуск тестов

### 4.1. Запуск всех тестов

```bash
python manage.py test
```

### 4.2. Запуск тестов конкретного приложения

```bash
python manage.py test myapp
```

### 4.3. Запуск конкретного теста

```bash
python manage.py test myapp.tests.MyModelTest
```

### 4.4. Запуск тестов с помощью pytest

```bash
pytest
```

## 5. Тестирование с использованием фикстур

Фикстуры позволяют загружать тестовые данные из файлов (например, JSON или YAML).

```python
from django.test import TestCase

class MyFixtureTest(TestCase):
    fixtures = ['my_fixture.json']

    def test_fixture_loading(self):
        model = MyModel.objects.get(id=1)
        self.assertEqual(model.name, "Fixture Model")
```

## 7. Тестирования внешних зависимостей

Для тестирования внешних зависимостей можно использовать библиотеку `unittest.mock`.

```python
from unittest.mock import patch
from django.test import TestCase
from myapp.views import my_view

class MyViewTest(TestCase):
    @patch('myapp.views.external_api_call')
    def test_my_view(self, mock_api_call):
        mock_api_call.return_value = "Mocked Response"
        response = self.client.get('/my-view-url/')
        self.assertEqual(response.content, b"Mocked Response")
```

## 8. Тестирование асинхронного кода

Для тестирования асинхронного кода можно использовать `pytest-asyncio`.

```python
import pytest
from myapp.async_utils import async_function

@pytest.mark.asyncio
async def test_async_function():
    result = await async_function()
    assert result == "expected result"
```

## 9. Покрытие кода тестами

Для измерения покрытия кода тестами можно использовать coverage.

```bash
pip install coverage
coverage run --source='.' manage.py test
coverage report
coverage html  # Генерация HTML-отчета
```

## 10. Полезные ссылки

- Официальная документация Django по тестированию - [ссылка](https://docs.djangoproject.com/en/stable/topics/testing/)
- Документация pytest - [ссылка](https://docs.pytest.org/en/latest/)
- Документация coverage - [ссылка](https://coverage.readthedocs.io/en/latest/)
