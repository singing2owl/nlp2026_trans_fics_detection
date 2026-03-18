# Original or Translated
## Translated Text Detection Based on Ficbook.group Data
### Authors: Ekaterina Fedorischeva, Polina Seregina, Tatiana Avdeeva
This is a repository for an NLP course project. 

Оригинал или перевод: выявление переведённых текстов на русском языке как задача бинарной классификации на материале платформы Ficbook.group

Data files (`origs_NER.rar` and `trans_NER.rar`) and the trained models are available [here](https://disk.360.yandex.ru/d/8gWRRL1SJn1N7A).

### Justification
Определение, является ли текст переводным, является важным как минимум для определения плагиата — не все авторы указывают, что часть их текста является переводом некоторого малоизвестного источника, а автоматические системы антиплагиата всё ещё не обучены решать такую задачу. Например, на данную тему существует работа 2025 года (октябрь) [[The Impact of Language Translation on Plagiarism Rates: Evidence from Turnitin, iThenticate, and Grammarly | Journal of Academic Ethics]](https://link.springer.com/article/10.1007/s10805-025-09681-5), в которой описаны результаты оценки самых популярных в англоязычных вузах детекторов плагиата — известно, что даже они показывают плохие результаты по детекции перевода. Для русского языка методы улучшения имеющегося алгоритма тоже существуют [[The Impact of Language Translation on Plagiarism Rates: Evidence from Turnitin, iThenticate, and Grammarly | Journal of Academic Ethics]](https://link.springer.com/article/10.1007/s10805-025-09681-5), но проблему нельзя считать решенной. Кроме того, известно, что использование переводных текстов мешает обучению LLM и ухудшает их результаты [[Emerging trends: translationese | Natural Language Processing | Cambridge Core]](https://www.cambridge.org/core/journals/natural-language-processing/article/emerging-trends-translationese/D39ADC5B44B06358A153F8926F88DD93). 

Мы рассматривали задачу детекции переведённых текстов как задачу бинарной классификации, хотя в одной из последних работ на эту тему [[Using Likelihood Ratios for Graded and Generalizable Measurement of Translationese - ACL Anthology]](https://aclanthology.org/2025.emnlp-main.633/) предлагается способ оценки вероятности того, что текст является переведённым.

### Данные
Фанфики с сайта КнигаФанфиков по фандому "Гарри Поттер": 855 оригинальных и 855 переведённых. Скрауленные данные находятся в файлах `origs_NER.rar` и `trans_NER.rar` в папке по [ссылке](https://disk.360.yandex.ru/d/8gWRRL1SJn1N7A).

### Гипотезы
Большинство существующих работ по выявлению “[translationese](https://en.wiktionary.org/wiki/translationese)” выполнены на корпусах литературных/профессиональных текстов, т. е. построены на предположении, что качество оригинального текста выше или равно качеству переведенного, в котором, напротив, может обнаружиться что-то неестественное. Однако мы предполагаем, что для нашего корпуса фанфиков ситуация может оказаться прямо противоположной: люди чаще переводят тексты хорошего качества, в то время как относительно общего массива текстов качественных фанфиков заметно меньше. Поэтому может получиться ситуация, что для нашего корпуса могут найтись другие закономерности определения, не является ли текст переводным. Также это может привести к следующей проблеме: модель может начать определять качественные фанфики как переводные, а некачественные — как оригинальные. В связи с этим, мы хотим проверить, можно ли как-то убрать этот bias, как и то, появится ли он вообще.

### Методы
В найденных нами более ранних работах для векторизации и классификации используются такие методы как SVM (см., например, [[Distinguishing translations from non-translations and identifying (in)direct translations’ source languages - UTUPub]](https://www.utupub.fi/handle/10024/160161)). Согласно другой работе [[[2210.13391] Explaining Translationese: why are Neural Classifiers Better and what do they Learn?]](https://arxiv.org/abs/2210.13391), с одной стороны, сам по себе метод SVM показывает себя хуже BERT’а, но при этом SVM, которому скормили эмбеддинги BERT’а, вполне конкурирует с чисто BERT’овыми классификаторами, так что всё зависит от параметров. Мы планируем взять уже обученную модель на основе BERT’а с huggingface и дообучить ее на наших данных.

### План работы

* Краулинг текстов с сайта [https://ficbook.group](https://ficbook.group) с сохранением меток о факте перевода
* Очистка данных от именованных сущностей (NER) с помощью Natasha
* Дообучение pre-trained-модели (cointegrated/rubert-tiny · Hugging Face) на задачу бинарной классификации “оригинал/перевод”
* Проверка гипотез

## Результаты
Мы попробовали обучить модели с помощью BERT и с помощью Tf-idf, чтобы сравнить, где качество выше. Самое высокое качество оказалось у модели на BERT'е, обучавшейся на текстах фанфиков по отрывкам с небольшим наложением (в 4 токена). Accuracy составила 0.9766. Тетрадка находится [здесь](https://github.com/singing2owl/nlp2026_trans_fics_detection/blob/main/transfics_bert_modified.ipynb).

