import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.util.*;
import javax.sound.sampled.*;
import java.io.*;

public class ReminderApp extends JFrame {
    private JComboBox<String> dayDropdown;
    private JComboBox<String> timeDropdown;
    private JComboBox<String> activityDropdown;
    private javax.swing.Timer timer;

    public ReminderApp() {
        setTitle("Reminder Application");
        setSize(400, 300);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLocationRelativeTo(null);

        JPanel panel = new JPanel();
        panel.setLayout(new GridLayout(5, 1));

        String[] days = {"Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"};
        dayDropdown = new JComboBox<>(days);
        panel.add(new JLabel("Select Day:"));
        panel.add(dayDropdown);

        String[] times = generateTimeOptions();
        timeDropdown = new JComboBox<>(times);
        panel.add(new JLabel("Select Time:"));
        panel.add(timeDropdown);

        String[] activities = {"Wake up", "Go to gym", "Breakfast", "Meetings", "Lunch", "Quick nap", "Go to library", "Dinner", "Go to sleep"};
        activityDropdown = new JComboBox<>(activities);
        panel.add(new JLabel("Select Activity:"));
        panel.add(activityDropdown);

        JButton setReminderButton = new JButton("Set Reminder");
        setReminderButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                setReminder();
            }
        });

        panel.add(setReminderButton);
        add(panel);

        setVisible(true);
    }

    private String[] generateTimeOptions() {
        String[] times = new String[24 * 2]; // 24 hours, 30-minute increments
        int index = 0;
        for (int hour = 0; hour < 24; hour++) {
            times[index++] = String.format("%02d:00", hour);
            times[index++] = String.format("%02d:30", hour);
        }
        return times;
    }

    private void setReminder() {
        String day = (String) dayDropdown.getSelectedItem();
        String time = (String) timeDropdown.getSelectedItem();
        String activity = (String) activityDropdown.getSelectedItem();

        Calendar calendar = Calendar.getInstance();
        String[] timeParts = time.split(":");
        calendar.set(Calendar.HOUR_OF_DAY, Integer.parseInt(timeParts[0]));
        calendar.set(Calendar.MINUTE, Integer.parseInt(timeParts[1]));
        calendar.set(Calendar.SECOND, 0);

       
        long delay = calendar.getTimeInMillis() - System.currentTimeMillis();
        if (delay < 0) {
            JOptionPane.showMessageDialog(this, "Selected time is in the past!", "Error", JOptionPane.ERROR_MESSAGE);
            return;
        }

        timer = new javax.swing.Timer((int) delay, new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                playSound();
                JOptionPane.showMessageDialog(null, "Time for: " + activity + " on " + day, "Reminder", JOptionPane.INFORMATION_MESSAGE);
            }
        });
        timer.setRepeats(false);
        timer.start();

        JOptionPane.showMessageDialog(this, "Reminder set for " + time + " on " + day + " for " + activity, "Success", JOptionPane.INFORMATION_MESSAGE);
    }

 
    private void playSound() {
        try {
            File soundFile = new File("chime.wav"); 
            AudioInputStream audioStream = AudioSystem.getAudioInputStream(soundFile);
            Clip clip = AudioSystem.getClip();
            clip.open(audioStream);
            clip.start();
        } catch (Exception ex) {
            ex.printStackTrace();
        }
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(new Runnable() {
            @Override
            public void run() {
                new ReminderApp();
            }
        });
    }
}
