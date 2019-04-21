Курсовая работа на тему: "Выявление свойств голоса с использованием рекурентных нейронных сетей"

Московский государственный университет имени М.В.Ломоносова

Выполнил студент 3-го курса механико-математического факультета кафедры  "Математических интеллектуальных систем" Киназаров Темирбек Галымжанович.

Научные руководители Часовских Анатолий Александрович, Ронжин Дмитрий Владимирович.


# Требования

* PyTorch 0.4.1
* python 3.5+
* numpy 1.15.4
* librosa 0.6.1

# Предобработка данных

Change the following config.yaml key to a regex containing all .WAV files in your downloaded TIMIT dataset. The TIMIT .WAV files must be converted to the standard format (RIFF) for the dvector_create.py script, but not for training the neural network.
```yaml
unprocessed_data: './TIMIT/*/*/*/*.wav'
```
Run the preprocessing script:
```
./data_preprocess.py 
```
Two folders will be created, train_tisv and test_tisv, containing .npy files containing numpy ndarrays of speaker utterances with a 90%/10% training/testing split.

# Training

To train the speaker verification model, run:
```
./train_speech_embedder.py 
```
with the following config.yaml key set to true:
```yaml
training: !!bool "true"
```
for testing, set the key value to:
```yaml
training: !!bool "false"
```
The log file and checkpoint save locations are controlled by the following values:
```yaml
log_file: './speech_id_checkpoint/Stats'
checkpoint_dir: './speech_id_checkpoint'
```
Only TI-SV is implemented.

# Получение D-вектора на другом датасете

После тренировки на полученный файл .model, который находится в speech_id_checkpoint, нужно прописать соответствующий путь в config.yaml. Далее запускаете dvector_create.py для создания numpy файлов train_sequence.npy, train_cluster_ids.npy, test_sequence.npy и test_cluster_ids.npy. 
Это уже готовые индивидуальные вектора с разметкой прнадлежности каждому диктору, которые можно будет использовать для обучения моделей сложнее.
Замечание: датасет должен быть подготовлен, т.е. должен содержать базу аудио для каждого спикера в отдельной папке. Деление подробнее в каждой папке не требуется. Нужно прописывать новый путь к датасету в файле config.yaml

В данной курсововой был использован датасет, как "чистый" датасет https://datashare.is.ed.ac.uk/handle/10283/1942
Также татасет дикторов на русском языке: http://www.repository.voxforge1.org/downloads/Russian/Trunk/Audio/Original/16kHz_16bit/

# Проверка корректной классификации
Можно воспользоваться Checkingdvectors.ipynb. Для нее доп. требование:  библиотека sklearn;

Результаты:
1. «Чистый» датасет англ.:
  29 дикторов по 350 — 400 фраз в большинстве одинакового содержания
  79% правильных ответов на тестовой выборке
2. Непрофессиональный датасет рус.:
  10 дикторов около 200 разнородных фраз на каждого
  80% - 85% правльных ответов на тестовой выборке(есть зависимость от тестовой выборки)
3. Малый датасет рус.:
  10 дикторов по 12 разнородных фраз
  79% - 95% правильных (большая зависимость от тестовой выборки)
