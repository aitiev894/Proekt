import random
import time
from PyQt5.QtWidgets import QApplication, QWidget, QLabel, QTextEdit, QPushButton, QVBoxLayout
from PyQt5.QtCore import Qt, QTimer

# фразы для теста скорости печати
sentences = [
    "Быстрая рыжая лиса перепрыгнула через ленивую собаку",
    "Python отличный язык программирования",
    "Искусственный интеллект меняет мир",
    "Я люблю создавать приложения с PyQt",
    "Современные технологии позволяют изменять будущее",
    "Тестирование программного обеспечения это важная часть разработки",
    "Не бойтесь ошибок они часть процесса обучения",
    "Мышление важно но важно ещё и действие",
    "В программировании важно не только писать код но и понимать его",
    "Каждый день новый шанс улучшить свои навыки",
    "Новые идеи появляются в самых неожиданных местах",
    "В будущем люди смогут путешествовать во времени", 
    "Технологии помогают делать мир более удобным",
    "Мы живём в эпоху быстрого развития науки и технологий",
    "Образование играет ключевую роль в формировании общества",
    "Каждый код должен быть понятен и доступен для чтения",
    "Не существует универсального подхода к решению всех задач",
    "Программирование это не просто набор команд это искусство",
    "Мыслим не только в рамках одной задачи но и глобально",
    "Вдохновение приходит в самые неожиданные моменты"
]

class TypingSpeedTest(QWidget):
    def __init__(self):
        super().__init__()
        self.initUI()

        self.setStyleSheet("""
            QWidget {
                background-color: #2b2b2b;
                color: #ffffff;
            }
            QTextEdit {
                background-color: #3c3f41;
                color: #ffffff;
                border: 2px solid #555;
                border-radius: 5px;
                padding: 10px;
                font-family: Consolas, monospace;
                font-size: 14px;
            }
            QPushButton {
                background-color: #3498db;
                color: white;
                border-radius: 5px;
                padding: 10px;
                font-size: 14px;
                font-weight: bold;
            }
            QPushButton:hover {
                background-color: #2980b9;
            }
            QLabel {
                font-size: 16px;
                font-weight: bold;
            }
        """)

    def initUI(self):
        self.setWindowTitle("Тест скорости печати")
        self.setGeometry(100, 100, 600, 400)

        self.sentence = random.choice(sentences)
        self.start_time = None
        self.error_count = 0

        self.timer = QTimer(self)
        self.timer.timeout.connect(self.update_timer)

        self.label = QLabel(self.sentence, self)
        self.label.setWordWrap(True)
        self.label.setAlignment(Qt.AlignmentFlag.AlignCenter)

        self.text_edit = QTextEdit(self)
        self.text_edit.textChanged.connect(self.check_text)

        self.start_button = QPushButton("Начать", self)
        self.start_button.clicked.connect(self.start_test)

        self.result_label = QLabel("", self)
        self.result_label.setAlignment(Qt.AlignmentFlag.AlignCenter)
        
        self.error_label = QLabel("Ошибок: 0", self)
        self.error_label.setAlignment(Qt.AlignmentFlag.AlignCenter)

        layout = QVBoxLayout()
        layout.addWidget(self.label)
        layout.addWidget(self.text_edit)
        layout.addWidget(self.start_button)
        layout.addWidget(self.result_label)
        layout.addWidget(self.error_label)
        self.setLayout(layout)

    def start_test(self):
        self.text_edit.clear()
        self.sentence = random.choice(sentences)
        self.label.setText(self.sentence)
        self.start_time = time.time()
        self.result_label.setText("")
        self.error_count = 0
        self.error_label.setText("Ошибок: 0")
        self.text_edit.setFocus()
        self.timer.start(1000)

    def update_timer(self):
        if self.start_time:
            elapsed_time = time.time() - self.start_time
            minutes = int(elapsed_time // 60)
            seconds = int(elapsed_time % 60)
            self.result_label.setText(f"Время: {minutes:02}:{seconds:02}")

    def check_text(self):
        if not self.start_time:
            return

        typed_text = self.text_edit.toPlainText()
        self.error_count = sum(1 for i in range(len(typed_text)) if i < len(self.sentence) and typed_text[i] != self.sentence[i])
        self.error_label.setText(f"Ошибок: {self.error_count}")
        
        if typed_text.strip() == self.sentence:
            self.timer.stop()
            elapsed_time = time.time() - self.start_time
            char_count = len(self.sentence)
            word_count = len(self.sentence.split())
            cpm = int((char_count / elapsed_time) * 60)
            wpm = int((word_count / elapsed_time) * 60)
            self.result_label.setText(f"Скорость: {wpm} слов/мин ({cpm} симв/мин) Время: {round(elapsed_time, 2)} секунд Ошибок: {self.error_count}")
            self.start_time = None

app = QApplication([])
window = TypingSpeedTest()
window.show()
app.exec()
