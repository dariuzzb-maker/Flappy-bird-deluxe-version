# Flappy-bird-deluxe-version
in day 1 i made the difficulties , skins , items , background and  sound 


Here is the code : 
import array
import json
import math
import os
import random
import shutil
import subprocess
import sys
import tempfile
import time
import tkinter as tk
import wave

WIDTH, HEIGHT = 480, 720
GROUND_Y = 630          
BIRD_X = 120            
PIPE_WIDTH = 70
PIPE_SPACING = 250      
FPS = 60


DIFFICULTIES = {
    "EASY":   {"gravity": 0.40, "flap": -7.2, "speed": 2.5, "gap": 210, "moving": 0.0, "points": 1, "color": (110, 230, 120)},
    "NORMAL": {"gravity": 0.45, "flap": -7.6, "speed": 2.9, "gap": 185, "moving": 0.2, "points": 2, "color": (255, 220, 80)},
    "HARD":   {"gravity": 0.50, "flap": -8.0, "speed": 3.4, "gap": 160, "moving": 0.4, "points": 3, "color": (255, 140, 60)},
    "INSANE": {"gravity": 0.56, "flap": -8.4, "speed": 4.0, "gap": 140, "moving": 0.6, "points": 4, "color": (255, 70, 70)},
}

SKINS = [
    ("YELLOW", (255, 205, 50), (255, 240, 130)),
    ("RED",    (235, 70, 85),  (255, 150, 160)),
    ("BLUE",   (70, 160, 240), (160, 210, 255)),
]

ITEMS = {
    "shield":  ("S",  (80, 170, 255)),    
    "double":  ("x2", (255, 200, 40)),    
    "shrink":  ("-",  (110, 225, 120)),   
    "mystery": ("?",  (255, 255, 255)),   
}

MYSTERY_RESULTS = ["shield", "double", "shrink", "bonus", "heavy", "fast"]
EFFECT_TIME = {"double": 600, "shrink": 600, "heavy": 300, "fast": 300}   # in frames
EFFECT_TEXT = {"shield": "SHIELD", "double": "DOUBLE POINTS", "shrink": "SHRINK",
               "heavy": "HEAVY BIRD...", "fast": "SPEED SURGE!", "bonus": "BONUS +20"}


SKY_COLORS = [(110, 195, 255), (255, 160, 110), (25, 30, 70)]

FONT_PIXELS = {24: 19, 32: 26, 44: 34, 70: 54, 100: 76}

SAVE_FILE = os.path.join(os.path.dirname(os.path.abspath(__file__)), "flappy_save.json")

def rgb(color):
    
    return "#%02x%02x%02x" % tuple(max(0, min(255, int(c))) for c in color)

SOUND_DEFS = {
    "flap":     ("square", 0.15, [(380, 620, 0.09)]),
    "score":    ("square", 0.18, [(700, 700, 0.06), (1050, 1050, 0.12)]),
    "coin":     ("sine",   0.30, [(1300, 1300, 0.05), (1750, 1750, 0.10)]),
    "powerup":  ("saw",    0.18, [(440, 440, 0.05), (554, 554, 0.05), (659, 659, 0.05), (880, 880, 0.14)]),
    "bad":      ("saw",    0.22, [(400, 110, 0.38)]),
    "dash":     ("saw",    0.12, [(200, 1000, 0.28)]),
    "shield":   ("sine",   0.40, [(1600, 200, 0.30)]),
    "hit":      ("noise",  0.45, [(1, 1, 0.35)]),
    "gameover": ("tri",    0.40, [(523, 523, 0.15), (494, 494, 0.15), (440, 440, 0.15), (349, 349, 0.45)]),
    "select":   ("square", 0.12, [(900, 900, 0.04)]),
}
