import sys
import numpy as np
import sounddevice as sd
from scipy.io.wavfile import write, read
from PyQt6.QtWidgets import (QApplication, QWidget, QPushButton, QVBoxLayout,
                             QLabel, QFileDialog, QHBoxLayout, QListWidget)
from PyQt6.QtCore import Qt

class AudioTrack:
    def __init__(self, name):
        self.name = name
        self.data = None
        self.samplerate = 44100
        self.filename = None

class DAW(QWidget):
    def __init__(self):
        super().__init__()
        self.setWindowTitle("Aidan DAW - v0.1 Alpha")
        self.setGeometry(100, 100, 1000, 600)

        self.tracks = []
        self.recording = False
        self.current_track = None

        self.layout = QVBoxLayout()
        self.track_list = QListWidget()
        self.layout.addWidget(self.track_list)

        # Control Buttons
        self.buttons_layout = QHBoxLayout()

        self.new_track_button = QPushButton("New Track")
        self.new_track_button.clicked.connect(self.create_new_track)
        self.buttons_layout.addWidget(self.new_track_button)

        self.record_button = QPushButton("Record")
        self.record_button.clicked.connect(self.record_audio)
        self.buttons_layout.addWidget(self.record_button)

        self.play_button = QPushButton("Play")
        self.play_button.clicked.connect(self.play_audio)
        self.buttons_layout.addWidget(self.play_button)

        self.save_button = QPushButton("Save Project")
        self.save_button.clicked.connect(self.save_project)
        self.buttons_layout.addWidget(self.save_button)

        self.load_button = QPushButton("Load Project")
        self.load_button.clicked.connect(self.load_project)
        self.buttons_layout.addWidget(self.load_button)

        self.layout.addLayout(self.buttons_layout)
        self.setLayout(self.layout)

    def create_new_track(self):
        track_name = f"Track {len(self.tracks) + 1}"
        track = AudioTrack(track_name)
        self.tracks.append(track)
        self.track_list.addItem(track_name)
        self.track_list.setCurrentRow(len(self.tracks) - 1)
        self.current_track = track
        print(f"Created {track_name}")

    def record_audio(self):
        if not self.current_track:
            return

        if not self.recording:
            self.record_button.setText("Stop Recording")
            self.recording = True
            print("Recording started...")
            self.current_track.data = sd.rec(int(5 * self.current_track.samplerate), samplerate=self.current_track.samplerate, channels=1)
        else:
            sd.stop()
            self.recording = False
            self.record_button.setText("Record")
            print("Recording stopped.")

    def play_audio(self):
        selected_row = self.track_list.currentRow()
        if selected_row == -1:
            return
        track = self.tracks[selected_row]
        if track.data is not None:
            print(f"Playing {track.name}")
            sd.play(track.data, track.samplerate)
            sd.wait()

    def save_project(self):
        save_path, _ = QFileDialog.getSaveFileName(self, "Save Project", "", "DAW Project (*.daw)")
        if not save_path:
            return
        print(f"Saving project to {save_path}")
        np.savez(save_path, *[track.data for track in self.tracks])

    def load_project(self):
        load_path, _ = QFileDialog.getOpenFileName(self, "Load Project", "", "DAW Project (*.daw)")
        if not load_path:
            return
        print(f"Loading project from {load_path}")
        data = np.load(load_path)
        self.tracks.clear()
        self.track_list.clear()
        for key in data.files:
            track = AudioTrack(f"Track {len(self.tracks)+1}")
            track.data = data[key]
            self.tracks.append(track)
            self.track_list.addItem(track.name)

if __name__ == "__main__":
    app = QApplication(sys.argv)
    daw = DAW()
    daw.show()
    sys.exit(app.exec())
