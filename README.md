package quiz.application;

import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

public class QuizApplicationWithImages extends JFrame implements ActionListener {

    // Components for different pages
    JButton start, next, submit, retry, finish;
    JLabel question, timerLabel, backgroundLabel, resultMessageLabel, motivationMessageLabel;
    JRadioButton option1, option2, option3, option4;
    ButtonGroup optionsGroup;
    JTextField nameField;

    // Variables for quiz logic
    String[][] questions = {
            {"What is the capital of France?", "Paris", "London", "Berlin", "Rome", "Paris"},
            {"Which programming language is platform-independent?", "Python", "C++", "Java", "Ruby", "Java"},
            {"Who developed the theory of relativity?", "Newton", "Einstein", "Galileo", "Tesla", "Einstein"},
            {"What is the largest planet in our solar system?", "Earth", "Venus", "Jupiter", "Mars", "Jupiter"},
            {"Which is the longest river in the world?", "Amazon", "Nile", "Yangtze", "Mississippi", "Nile"},
            {"What is the square root of 16?", "4", "5", "6", "8", "4"},
            {"Who painted the Mona Lisa?", "Picasso", "Van Gogh", "Da Vinci", "Michelangelo", "Da Vinci"},
            {"Which is the smallest country in the world?", "USA", "China", "Vatican City", "India", "Vatican City"},
            {"What is the fastest animal on land?", "Lion", "Cheetah", "Elephant", "Horse", "Cheetah"},
            {"Which is the largest ocean on Earth?", "Atlantic", "Indian", "Arctic", "Pacific", "Pacific"}
    };

    int currentQuestion = 0;
    int score = 0;
    int timer = 15; // 15 seconds per question
    Timer quizTimer;

    public QuizApplicationWithImages() {
        // Initial Frame setup
        setTitle("Quiz Application");
        setSize(800, 600);
        setLayout(null);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        // Set background image for Login Page
        ImageIcon backgroundImage = new ImageIcon("C:\\Users\\sony\\Desktop\\JAVA QUIZ\\background.jpg"); // Replace with your image path
        backgroundLabel = new JLabel(backgroundImage);
        backgroundLabel.setBounds(0, 0, 800, 600);
        add(backgroundLabel);

        // Login Page Components
        JLabel welcome = new JLabel("Welcome to the Quiz");
        welcome.setFont(new Font("Serif", Font.BOLD, 32));
        welcome.setForeground(Color.WHITE);
        welcome.setBounds(250, 50, 400, 40);
        backgroundLabel.add(welcome);

        JLabel nameLabel = new JLabel("Enter Your Name:");
        nameLabel.setFont(new Font("Arial", Font.BOLD, 17));
        nameLabel.setForeground(Color.WHITE);
        nameLabel.setBounds(250, 150, 150, 30);
        backgroundLabel.add(nameLabel);

        nameField = new JTextField();
        nameField.setBounds(400, 150, 200, 30);
        backgroundLabel.add(nameField);

        start = new JButton("Start");
        start.setBounds(350, 250, 100, 30);
        start.addActionListener(this);
        backgroundLabel.add(start);

        setVisible(true);
    }

    public void showRules(String name) {
        getContentPane().removeAll(); // Clear previous components
        setLayout(null);

        // Set background image for Rules page
        ImageIcon rulesBackground = new ImageIcon("C:\\Users\\sony\\Desktop\\JAVA QUIZ\\background2.jpg"); // Replace with your image path
        backgroundLabel = new JLabel(rulesBackground);
        backgroundLabel.setBounds(0, 0, 800, 600);
        add(backgroundLabel);

        JLabel rulesLabel = new JLabel("<html>Welcome, " + name + "<br>Rules:<br>1. Each question has one correct answer.<br>2. You have 15 seconds per question.<br>3. No negative marking.</html>");
        rulesLabel.setFont(new Font("Arial", Font.PLAIN, 18));
        rulesLabel.setForeground(Color.WHITE);
        rulesLabel.setBounds(50, 50, 700, 200);
        backgroundLabel.add(rulesLabel);

        JButton proceed = new JButton("Proceed to Quiz");
        proceed.setBounds(300, 400, 200, 40);
        proceed.addActionListener(e -> startQuiz());
        backgroundLabel.add(proceed);

        revalidate();
        repaint();
    }

    public void startQuiz() {
        getContentPane().removeAll(); // Clear previous components
        setLayout(null);

        // Set background image for Question Page - Full screen
        ImageIcon questionBackground = new ImageIcon("C:\\Users\\sony\\Desktop\\JAVA QUIZ\\question.jpg"); // Path to your image
        backgroundLabel = new JLabel(questionBackground);
        backgroundLabel.setBounds(0, 0, getWidth(), getHeight());
        backgroundLabel.setIcon(new ImageIcon(questionBackground.getImage().getScaledInstance(getWidth(), getHeight(), Image.SCALE_SMOOTH)));
        add(backgroundLabel);

        // Question Label
        question = new JLabel();
        question.setFont(new Font("Arial", Font.BOLD, 20));
        question.setForeground(Color.WHITE);
        question.setBounds(50, 50, 700, 30);
        backgroundLabel.add(question);

        // Option Buttons
        option1 = new JRadioButton();
        option1.setBounds(50, 120, 700, 30);
        option1.setOpaque(false);
        option1.setForeground(Color.WHITE);
        backgroundLabel.add(option1);

        option2 = new JRadioButton();
        option2.setBounds(50, 180, 700, 30);
        option2.setOpaque(false);
        option2.setForeground(Color.WHITE);
        backgroundLabel.add(option2);

        option3 = new JRadioButton();
        option3.setBounds(50, 240, 700, 30);
        option3.setOpaque(false);
        option3.setForeground(Color.WHITE);
        backgroundLabel.add(option3);

        option4 = new JRadioButton();
        option4.setBounds(50, 300, 700, 30);
        option4.setOpaque(false);
        option4.setForeground(Color.WHITE);
        backgroundLabel.add(option4);

        optionsGroup = new ButtonGroup();
        optionsGroup.add(option1);
        optionsGroup.add(option2);
        optionsGroup.add(option3);
        optionsGroup.add(option4);

        // Buttons
        next = new JButton("Next");
        next.setBounds(150, 400, 100, 30);
        next.addActionListener(this);
        backgroundLabel.add(next);

        submit = new JButton("Submit");
        submit.setBounds(300, 400, 100, 30);
        submit.addActionListener(this);
        submit.setEnabled(false);
        backgroundLabel.add(submit);

        // Timer Label
        timerLabel = new JLabel("Time left: " + timer);
        timerLabel.setFont(new Font("Arial", Font.BOLD, 18));
        timerLabel.setForeground(Color.WHITE);
        timerLabel.setBounds(650, 20, 120, 30);
        backgroundLabel.add(timerLabel);

        displayQuestion();
        revalidate();
        repaint();
    }

    public void displayQuestion() {
        if (quizTimer != null) {
            quizTimer.stop(); // Stop the previous timer
        }
        timer = 15; // Reset timer
        timerLabel.setText("Time left: " + timer); // Reset timer label

        if (currentQuestion < questions.length) {
            question.setText("Q" + (currentQuestion + 1) + ": " + questions[currentQuestion][0]);
            option1.setText(questions[currentQuestion][1]);
            option2.setText(questions[currentQuestion][2]);
            option3.setText(questions[currentQuestion][3]);
            option4.setText(questions[currentQuestion][4]);
            optionsGroup.clearSelection();

            startTimer(); // Start the timer for the new question
        } else {
            showResult();
        }
    }

    public void startTimer() {
        quizTimer = new Timer(1000, e -> {
            timer--;
            timerLabel.setText("Time left: " + timer);
            if (timer <= 0) {
                quizTimer.stop();
                next.doClick(); // Auto-click Next
            }
        });
        quizTimer.start();
    }

    public void calculateScore() {
        String selectedOption = null;
        if (option1.isSelected()) selectedOption = option1.getText();
        if (option2.isSelected()) selectedOption = option2.getText();
        if (option3.isSelected()) selectedOption = option3.getText();
        if (option4.isSelected()) selectedOption = option4.getText();

        if (selectedOption != null) {
            if (selectedOption.equals(questions[currentQuestion][5])) {
                score++;
            }
        }
    }

    public void showResult() {
        if (quizTimer != null) {
            quizTimer.stop(); // Stop timer when showing results
        }

        // Remove previous components and show result screen
        getContentPane().removeAll();
        setLayout(null);

        // Set background image for Final Score page
        ImageIcon finalScoreBackground = new ImageIcon("C:\\Users\\sony\\Desktop\\JAVA QUIZ\\background2.jpg"); // Path to your image
        backgroundLabel = new JLabel(finalScoreBackground);
        backgroundLabel.setBounds(0, 0, 800, 600);
        backgroundLabel.setIcon(new ImageIcon(finalScoreBackground.getImage().getScaledInstance(getWidth(), getHeight(), Image.SCALE_SMOOTH)));
        add(backgroundLabel);

        // Show Score
        resultMessageLabel = new JLabel("You Scored: " + score + " / " + questions.length);
        resultMessageLabel.setFont(new Font("Arial", Font.BOLD, 30));
        resultMessageLabel.setForeground(Color.WHITE);
        resultMessageLabel.setBounds(250, 200, 300, 50);
        backgroundLabel.add(resultMessageLabel);

        motivationMessageLabel = new JLabel(score > 7 ? "Great Job!" : "Try Next Time!");
        motivationMessageLabel.setFont(new Font("Arial", Font.BOLD, 20));
        motivationMessageLabel.setForeground(Color.WHITE);
        motivationMessageLabel.setBounds(300, 300, 200, 50);
        backgroundLabel.add(motivationMessageLabel);

        retry = new JButton("Retry");
        retry.setBounds(250, 400, 100, 30);
        retry.addActionListener(e -> {
            score = 0;
            currentQuestion = 0;
            startQuiz();
        });
        backgroundLabel.add(retry);

        finish = new JButton("Finish");
        finish.setBounds(400, 400, 100, 30);
        finish.addActionListener(e -> System.exit(0));
        backgroundLabel.add(finish);

        revalidate();
        repaint();
    }

    @Override
    public void actionPerformed(ActionEvent e) {
        if (e.getSource() == start) {
            String name = nameField.getText();
            if (name.isEmpty()) {
                JOptionPane.showMessageDialog(this, "Please enter your name.");
            } else {
                showRules(name);
            }
        } else if (e.getSource() == next) {
            calculateScore();
            currentQuestion++;
            displayQuestion();
            submit.setEnabled(true); // Enable Submit after Next
        } else if (e.getSource() == submit) {
            calculateScore();
            showResult();
        }
    }

    public static void main(String[] args) {
        new QuizApplicationWithImages();
    }
}
