import javax.swing.*;
import javax.swing.border.TitledBorder;
import java.awt.*;
import java.util.ArrayList;
import java.util.Comparator;
import java.util.List;
import java.util.HashMap;
import java.util.Map;

/**
 * Catan Score Tracker - Professional Edition
 * Developed by: ARDA YALIN UCAR
 * Features: Turn-based logic, City-Settlement conversion, Global Rankings
 */
public class CatanTracker {

    public static final int WINNING_POINTS = 10;
    public static List<Player> players = new ArrayList<>();
    public static int currentPlayerIndex = 0;

    public static JFrame frame;
    public static JPanel mainPanel;

    // Color mapping for UI backgrounds
    private static final Map<String, Color> colorMap = new HashMap<>();

    static {
        colorMap.put("RED", new Color(255, 100, 100));
        colorMap.put("BLUE", new Color(100, 150, 255));
        colorMap.put("ORANGE", new Color(255, 180, 50));
        colorMap.put("WHITE", Color.WHITE);
        colorMap.put("GREEN", new Color(100, 200, 100));
        colorMap.put("BROWN", new Color(150, 75, 0));
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(CatanTracker::setupGame);
    }

    public static void setupGame() {
        try {
            UIManager.setLookAndFeel(UIManager.getSystemLookAndFeelClassName());
            String countStr = JOptionPane.showInputDialog("How many players? (3-4)");
            if (countStr == null) System.exit(0);
            int playerCount = Integer.parseInt(countStr);

            String[] availableColors = {"RED", "BLUE", "ORANGE", "WHITE", "GREEN", "BROWN"};

            for (int i = 0; i < playerCount; i++) {
                String name = JOptionPane.showInputDialog("Player " + (i + 1) + " Name:");
                if (name == null || name.isEmpty()) name = "Player " + (i + 1);

                String color = (String) JOptionPane.showInputDialog(null, "Choose Color:",
                        "Color Selection", JOptionPane.QUESTION_MESSAGE, null, availableColors, availableColors[i]);

                players.add(new Player(name, color != null ? color : "WHITE"));
            }
            initUI();
        } catch (Exception e) {
            JOptionPane.showMessageDialog(null, "Invalid input! Please enter a number.");
            setupGame();
        }
    }

    public static void initUI() {
        frame = new JFrame("CATAN BOARD - Pro Tracker");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setSize(550, 800); // Slightly increased height for footer
        frame.setLocationRelativeTo(null);

        mainPanel = new JPanel();
        mainPanel.setLayout(new BoxLayout(mainPanel, BoxLayout.Y_AXIS));

        // --- Header Section ---
        JPanel header = new JPanel();
        header.setBackground(new Color(44, 62, 80)); // Dark sleek header
        header.setMaximumSize(new Dimension(Integer.MAX_VALUE, 60)); // Fixed height
        JLabel title = new JLabel(" C A T A N ");
        title.setFont(new Font("Serif", Font.BOLD, 36));
        title.setForeground(new Color(241, 196, 15)); // Gold color
        header.add(title);

        // --- Main Content (Scrollable) ---
        JPanel contentPanel = new JPanel();
        contentPanel.setLayout(new BoxLayout(contentPanel, BoxLayout.Y_AXIS));

        // Adding dynamic parts to the content panel
        updateContent(contentPanel);

        // --- Footer Section (Signature) ---
        JPanel footer = new JPanel(new FlowLayout(FlowLayout.CENTER));
        footer.setBackground(new Color(236, 240, 241)); // Light gray footer
        footer.setBorder(BorderFactory.createMatteBorder(1, 0, 0, 0, Color.LIGHT_GRAY)); // Top border
        footer.setMaximumSize(new Dimension(Integer.MAX_VALUE, 40)); // Fixed height

        JLabel developerLabel = new JLabel("Developed by ARDA YALIN UCAR");
        developerLabel.setFont(new Font("SansSerif", Font.ITALIC, 14));
        developerLabel.setForeground(Color.DARK_GRAY);
        footer.add(developerLabel);

        // Frame Layout
        frame.setLayout(new BorderLayout());
        frame.add(header, BorderLayout.NORTH);
        frame.add(new JScrollPane(contentPanel), BorderLayout.CENTER);
        frame.add(footer, BorderLayout.SOUTH);

        frame.setVisible(true);

        // Store contentPanel reference to update it later
        mainPanel = contentPanel;
    }

    public static void updateContent(JPanel panel) {
        panel.removeAll();
        checkSpecialPoints();

        Player current = players.get(currentPlayerIndex);
        Color playerBg = colorMap.getOrDefault(current.color.toUpperCase(), Color.LIGHT_GRAY);

        // --- Current Turn Info ---
        JPanel turnPanel = new JPanel(new BorderLayout());
        turnPanel.setBackground(playerBg);
        turnPanel.setBorder(BorderFactory.createEmptyBorder(10, 10, 10, 10));
        JLabel turnLabel = new JLabel("CURRENT TURN: " + current.name.toUpperCase(), SwingConstants.CENTER);
        turnLabel.setFont(new Font("SansSerif", Font.BOLD, 22));
        turnPanel.add(turnLabel, BorderLayout.CENTER);

        JButton nextBtn = new JButton("FINISH TURN");
        nextBtn.setFocusPainted(false);
        nextBtn.addActionListener(e -> nextTurn());
        turnPanel.add(nextBtn, BorderLayout.SOUTH);

        panel.add(turnPanel);

        // --- Stats & Controls Section ---
        JPanel controlPanel = new JPanel(new GridLayout(5, 2, 10, 10));
        controlPanel.setBackground(playerBg);
        controlPanel.setBorder(BorderFactory.createTitledBorder(
                BorderFactory.createEtchedBorder(), "PLAYER STATS", TitledBorder.LEFT, TitledBorder.TOP,
                new Font("SansSerif", Font.BOLD, 14), Color.DARK_GRAY));

        addControlRow(controlPanel, "Settlements (1pt)", current, "settlement");
        addControlRow(controlPanel, "Cities (2pts)", current, "city");
        addControlRow(controlPanel, "Roads (Min 5 for bonus)", current, "road");
        addControlRow(controlPanel, "Knights (Min 3 for bonus)", current, "knight");
        addControlRow(controlPanel, "Victory Point Cards", current, "victory");

        panel.add(controlPanel);

        // --- Global Scoreboard ---
        JPanel scoreTable = new JPanel();
        scoreTable.setLayout(new BoxLayout(scoreTable, BoxLayout.Y_AXIS));
        scoreTable.setBorder(BorderFactory.createTitledBorder("GLOBAL RANKINGS"));
        scoreTable.setBackground(Color.WHITE);

        for (Player pl : players) {
            String badge = "";
            if (pl.hasLongestRoad) badge += " [LONGEST ROAD]";
            if (pl.hasLargestArmy) badge += " [LARGEST ARMY]";

            JLabel scoreLabel = new JLabel(pl.name + " (" + pl.color + "): " + pl.calculatePoints() + " pts" + badge);
            scoreLabel.setFont(new Font("SansSerif", pl == current ? Font.BOLD : Font.PLAIN, 15));
            scoreTable.add(scoreLabel);
        }
        panel.add(scoreTable);

        panel.revalidate();
        panel.repaint();

        if (current.calculatePoints() >= WINNING_POINTS) {
            endGame(current);
        }
    }

    private static void addControlRow(JPanel panel, String label, Player p, String type) {
        JLabel lbl = new JLabel(label);
        lbl.setFont(new Font("SansSerif", Font.BOLD, 12));
        panel.add(lbl);

        JPanel btnPanel = new JPanel(new FlowLayout(FlowLayout.RIGHT));
        btnPanel.setOpaque(false);

        JButton minus = new JButton("-");
        JButton plus = new JButton("+");

        int val = switch (type) {
            case "settlement" -> p.settlements;
            case "city" -> p.cities;
            case "road" -> p.roads;
            case "knight" -> p.knights;
            case "victory" -> p.extraPoints;
            default -> 0;
        };

        JLabel valLabel = new JLabel(String.valueOf(val));
        valLabel.setFont(new Font("SansSerif", Font.BOLD, 16));
        valLabel.setPreferredSize(new Dimension(40, 25));
        valLabel.setHorizontalAlignment(SwingConstants.CENTER);

        minus.addActionListener(e -> { p.update(type, -1); updateDisplay(); });
        plus.addActionListener(e -> { p.update(type, 1); updateDisplay(); });

        btnPanel.add(minus);
        btnPanel.add(valLabel);
        btnPanel.add(plus);
        panel.add(btnPanel);
    }

    // Helper to trigger update on the right panel
    public static void updateDisplay() {
        if (mainPanel != null) {
            updateContent(mainPanel);
        }
    }

    public static void nextTurn() {
        currentPlayerIndex = (currentPlayerIndex + 1) % players.size();
        updateDisplay();
    }

    public static void checkSpecialPoints() {
        players.forEach(p -> p.hasLongestRoad = false);
        players.stream().filter(p -> p.roads >= 5)
                .max(Comparator.comparingInt(p -> p.roads))
                .ifPresent(p -> p.hasLongestRoad = true);

        players.forEach(p -> p.hasLargestArmy = false);
        players.stream().filter(p -> p.knights >= 3)
                .max(Comparator.comparingInt(p -> p.knights))
                .ifPresent(p -> p.hasLargestArmy = true);
    }

    public static void endGame(Player winner) {
        players.sort((p1, p2) -> Integer.compare(p2.calculatePoints(), p1.calculatePoints()));
        StringBuilder sb = new StringBuilder("🏆 WINNER: " + winner.name.toUpperCase() + " 🏆\n\nFinal Rankings:\n");
        for (int i = 0; i < players.size(); i++) {
            sb.append(i + 1).append(". ").append(players.get(i).name).append(" - ").append(players.get(i).calculatePoints()).append(" pts\n");
        }
        JOptionPane.showMessageDialog(frame, sb.toString(), "Game Over", JOptionPane.INFORMATION_MESSAGE);
        System.exit(0);
    }

    static class Player {
        String name, color;
        int settlements = 2, cities = 0, roads = 2, knights = 0, extraPoints = 0;
        boolean hasLongestRoad = false, hasLargestArmy = false;

        Player(String name, String color) {
            this.name = name;
            this.color = color;
        }

        int calculatePoints() {
            int total = (settlements * 1) + (cities * 2) + extraPoints;
            if (hasLongestRoad) total += 2;
            if (hasLargestArmy) total += 2;
            return total;
        }

        void update(String type, int amount) {
            switch (type) {
                case "settlement" -> settlements = Math.max(0, settlements + amount);
                case "city" -> {
                    if (amount > 0) { // Build City
                        if (settlements > 0) {
                            cities++;
                            settlements--;
                        } else {
                            JOptionPane.showMessageDialog(null, "You need a Settlement to upgrade to a City!");
                        }
                    } else { // Downgrade City
                        if (cities > 0) {
                            cities--;
                            settlements++;
                        }
                    }
                }
                case "road" -> roads = Math.max(0, roads + amount);
                case "knight" -> knights = Math.max(0, knights + amount);
                case "victory" -> extraPoints = Math.max(0, extraPoints + amount);
            }
        }
    }
}
