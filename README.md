# Quiz-System
Built a quiz system using Singleton, Strategy, and Decorator patterns. Allowed users to select difficulty, track scores, and receive feedback. Designed modular architecture with reusable components.
import time

import time
import random

# Singleton Pattern for Quiz Manager 
class QuizManager:
    _instance = None

    def __new__(cls):
        if cls._instance is None:
            cls._instance = super(QuizManager, cls).__new__(cls)
            cls._instance.quizzes = []
        return cls._instance
    
    def add_quiz(self, quiz):
        self.quizzes.append(quiz)

    def start_quiz(self, strategy):
        for idx, quiz in enumerate(self.quizzes):
            print(f"{idx + 1}. {quiz.title}")
        choice = int(input("Select quiz number: ")) - 1
        selected_quiz = self.quizzes[choice]
        selected_quiz.start(strategy)

# Strategy Pattern for scoring
class ScoreStrategy:
    def calculate_score(self, correct_answers, total_questions):
        pass

class EasyScoreStrategy(ScoreStrategy):
    def calculate_score(self, correct_answers, total_questions):
        return correct_answers * 5

class HardScoreStrategy(ScoreStrategy):
    def calculate_score(self, correct_answers, total_questions):
        return correct_answers * 10

#Base Question Class
class Question:
    def __init__(self, text, answer):
        self.text = text
        self.answer = answer
    
    def display(self):
        print(self.text)
    
    def check_answer(self, user_answer):
        return self.answer.lower() == user_answer.lower()

# Quiz Class
class Quiz:
    def __init__(self, title):
        self.title = title
        self.questions = []
    
    def add_question(self, question):
        self.questions.append(question)

    def start(self, strategy):
        print(f"\nStarting Quiz: {self.title}")
        correct_answers = 0
        random.shuffle(self.questions)
        for question in self.questions:
            question.display()
            answer = input("Your answer: ")
            if question.check_answer(answer):
                correct_answers += 1
            total_questions = len(self.questions)
            score = strategy.calculate_score(correct_answers, total_questions)
            print(f"Your score is : {score}")
    

# Decorator Pattern for enhancing quiz
class TimerQuizDecorator:
    def __init__(self, quiz):
        self.quiz = quiz
    
    def start(self, strategy):
        start_time = time.time()
        self.quiz.start(strategy)
        end_time = time.time()
        print(f"Time taken: {end_time - start_time:.2f} seconds\n")
    
    def __getattr__(self, name):
        return getattr(self.quiz, name)

#Demo
if __name__ == "__main__":
    quiz_manager = QuizManager()

    # Create quizzes
    quiz1 = Quiz("General knowledge Quiz")
    quiz1.add_question(Question("What is the capital of France?", "Paris"))
    quiz1.add_question(Question("What is the capital of China?", "Beijing"))

    quiz2 = Quiz("Math Quiz")
    quiz2.add_question(Question("What is 5 + 7", "12"))
    quiz2.add_question(Question("What is 9 * 3", "27"))

    quiz3 = Quiz("Computer Quiz")
    quiz3.add_question(Question("Collection of 8 bits is", "Byte"))
    quiz3.add_question(Question("Who is the founder of Facebook", "Mark Zuckerberg"))
    quiz3.add_question(Question("What does CPU stand for", "Central Processing Unit"))

    # Decorate quizzes
    decorated_quiz1 = TimerQuizDecorator(quiz1)
    decorated_quiz2 = TimerQuizDecorator(quiz2)
    decorated_quiz3 = TimerQuizDecorator(quiz3)

    # Add quizzes
    quiz_manager.add_quiz(decorated_quiz1)
    quiz_manager.add_quiz(decorated_quiz2)
    quiz_manager.add_quiz(decorated_quiz3)

    # Choose Scoring Strategy
    difficulty = input("Choose difficulty (easy/hard): ").lower()
    if difficulty == 'easy':
        strategy = EasyScoreStrategy()
    else:
        strategy = HardScoreStrategy()

    # Start quiz
    quiz_manager.start_quiz(strategy)




     
