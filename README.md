import random
import time

class SupermarketSimulator:
    def __init__(self):
        self.money = 1000.00
        self.day = 1
        self.inventory = {
            "Bread": {"stock": 10, "cost": 1.20, "price": 2.50},
            "Milk":  {"stock": 8,  "cost": 1.50, "price": 3.20},
            "Eggs":  {"stock": 12, "cost": 2.00, "price": 4.00},
            "Cereal":{"stock": 5,  "cost": 3.00, "price": 5.50}
        }
        self.log = []

    def display_status(self):
        print(f"\n=================== DAY {self.day} ===================")
        print(f"Store Balance: ${self.money:.2f}")
        print("------------------------------------------------")
        print(f"{'Product':<10} | {'Stock':<6} | {'Cost Price':<10} | {'Your Price':<10}")
        print("------------------------------------------------")
        for item, info in self.inventory.items():
            print(f"{item:<10} | {info['stock']:<6} | ${info['cost']:<9.2f} | ${info['price']:<9.2f}")
        print("================================================")

    def order_stock(self):
        print("\n--- Wholesaler Market ---")
        for i, (item, info) in enumerate(self.inventory.items(), 1):
            print(f"{i}. {item} (Cost: ${info['cost']:.2f})")
        
        try:
            choice = int(input("Select product number to buy (0 to cancel): "))
            if choice == 0: return
            item_name = list(self.inventory.keys())[choice - 1]
            amount = int(input(f"How many units of {item_name} do you want to order? "))
            
            total_cost = self.inventory[item_name]['cost'] * amount
            if total_cost > self.money:
                print("❌ Insufficient funds!")
            else:
                self.money -= total_cost
                self.inventory[item_name]['stock'] += amount
                print(f"📦 Successfully stocked {amount}x {item_name} for ${total_cost:.2f}")
        except (ValueError, IndexError):
            print("❌ Invalid input.")

    def adjust_prices(self):
        print("\n--- Pricing Management ---")
        for i, item in enumerate(self.inventory.keys(), 1):
            print(f"{i}. {item} (Current Price: ${self.inventory[item]['price']:.2f})")
            
        try:
            choice = int(input("Select product number to reprice (0 to cancel): "))
            if choice == 0: return
            item_name = list(self.inventory.keys())[choice - 1]
            new_price = float(input(f"Set new selling price for {item_name}: $"))
            
            if new_price <= 0:
                print("❌ Price must be positive!")
            else:
                self.inventory[item_name]['price'] = new_price
                print(f"🏷️ {item_name} price updated to ${new_price:.2f}")
        except (ValueError, IndexError):
            print("❌ Invalid input.")

    def open_store(self):
        print("\n🔔 Store is now OPEN! Customers are walking in...")
        time.sleep(1)
        
        customers = random.randint(3, 7)
        daily_revenue = 0
        items_sold = 0
        
        for c in range(1, customers + 1):
            print(f"\n👤 Customer #{c} is browsing...")
            time.sleep(0.5)
            
            # Customer decides what to buy
            for item, info in self.inventory.items():
                if info['stock'] > 0:
                    # Customers complain or refuse to buy if markup is too high
                    max_acceptable = info['cost'] * 2.2 
                    if info['price'] > max_acceptable:
                        print(f"  - Refused to buy {item}: 'Too expensive!'")
                        continue
                        
                    if random.choice([True, False]): # 50% chance to want the item
                        buy_qty = random.randint(1, min(3, info['stock']))
                        info['stock'] -= buy_qty
                        revenue = info['price'] * buy_qty
                        daily_revenue += revenue
                        items_sold += buy_qty
                        print(f"  + Bought {buy_qty}x {item} for ${revenue:.2f}")
                        
        self.money += daily_revenue
        print("\n------------------------------------------------")
        print(f"🔔 Store CLOSED. Daily Summary:")
        print(f"Total items sold: {items_sold}")
        print(f"Revenue collected: ${daily_revenue:.2f}")
        self.day += 1
        input("\nPress Enter to start the next day...")

    def run(self):
        while True:
            self.display_status()
            print("What would you like to do?")
            print("1. Order Stock (Wholesaler)")
            print("2. Adjust Item Prices")
            print("3. Open Store for the Day")
            print("4. Exit Game")
            
            choice = input("Enter choice (1-4): ")
            if choice == '1':
                self.order_stock()
            elif choice == '2':
                self.adjust_prices()
            elif choice == '3':
                self.open_store()
            elif choice == '4':
                print("Thanks for playing Supermarket Simulator!")
                break
            else:
                print("Invalid choice.")

if __name__ == "__main__":
    game = SupermarketSimulator()
    game.run()
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Welding Game</title>
  <style>
    :root {
      --bg1: #0d1117;
      --bg2: #1f2d3d;
      --panel: rgba(20, 24, 32, 0.72);
      --text: #ebf3ff;
      --muted: #9bb4d4;
      --accent: #ffb703;
      --danger: #ff5a5f;
      --steel1: #9aa3af;
      --steel2: #676e76;
      --steel3: #414b55;
      --water: rgba(71, 146, 255, 0.42);
      --waterStrong: rgba(47, 112, 233, 0.76);
    }

    * { box-sizing: border-box; }

    body {
      margin: 0;
      background:
        radial-gradient(circle at top, #20314d 0%, #121821 38%, #080b10 100%);
      color: var(--text);
      font-family: Arial, Helvetica, sans-serif;
      display: flex;
      align-items: center;
      justify-content: center;
      min-height: 100vh;
      overflow: hidden;
    }

    .frame {
      position: relative;
      width: min(96vw, 1000px);
      height: min(76vh, 620px);
      border-radius: 18px;
      overflow: hidden;
      box-shadow:
        0 20px 60px rgba(0,0,0,0.55),
        inset 0 0 0 2px rgba(255,255,255,0.08);
      background: #111821;
    }

    canvas {
      display: block;
      width: 100%;
      height: 100%;
      background: linear-gradient(#2d3d52, #111822 48%, #0d1117 100%);
    }

    .hud {
      position: absolute;
      top: 12px;
      left: 12px;
      right: 12px;
      display: flex;
      justify-content: space-between;
      pointer-events: none;
      font-size: 14px;
      font-weight: 700;
    }

    .status {
      background: var(--panel);
      border: 1px solid rgba(255,255,255,0.08);
      border-radius: 10px;
      padding: 8px 12px;
      backdrop-filter: blur(5px);
      box-shadow: 0 8px 20px rgba(0,0,0,0.15);
    }

    .status small {
      display: block;
      color: var(--muted);
      font-weight: 600;
      letter-spacing: 0.07em;
      text-transform: uppercase;
      margin-bottom: 2px;
    }

    .controls {
      position: absolute;
      left: 12px;
      bottom: 12px;
      display: flex;
      gap: 8px;
      flex-wrap: wrap;
      pointer-events: none;
    }

    .pill {
      background: rgba(10,16,26,0.7);
      border: 1px solid rgba(255,255,255,0.08);
      border-radius: 999px;
      padding: 7px 11px;
      font-size: 12px;
      color: var(--text);
      letter-spacing: 0.04em;
      text-transform: uppercase;
      font-weight: 700;
    }
  </style>
</head>
<body>
  <div class="frame">
    <canvas id="game"></canvas>

    <div class="hud">
      <div class="status">
        <small>Mode</small>
        <span id="modeText">Surface</span>
      </div>
      <div class="status">
        <small>Weld Progress</small>
        <span id="progressText">0%</span>
      </div>
    </div>

    <div class="controls">
      <div class="pill">1 = Surface</div>
      <div class="pill">2 = Underwater</div>
      <div class="pill">WASD move</div>
      <div class="pill">Hold mouse / Space to weld</div>
    </div>
  </div>

  <script>
    const canvas = document.getElementById("game");
    const ctx = canvas.getContext("2d");

    const modeText = document.getElementById("modeText");
    const progressText = document.getElementById("progressText");

    const state = {
      width: 1000,
      height: 620,
      mode: "surface",
      waterLevel: 0,
      current: 0,
      time: 0,
      goal: 0,
      sparks: [],
      bubbles: [],
      messages: []
    };

    const keys = {};
    const pointer = { x: 220, y: 220, down: false };

    const player = {
      x: 160,
      y: 170,
      vx: 0,
      vy: 0,
      radius: 16,
      speed: 240,
      weldPower: 1
    };

    const seams = [
      { x: 360, y: 200, length: 270, progress: 0, baseGlow: 0 },
      { x: 370, y: 320, length: 250, progress: 0, baseGlow: 0 }
    ];

    function setMode(m) {
      state.mode = m;
      state.waterLevel = m === "underwater" ? 350 : 0;
      state.current = m === "underwater" ? 42 : 0;
      modeText.textContent = m === "underwater" ? "Underwater" : "Surface";
    }

    function resize() {
      const rect = canvas.getBoundingClientRect();
      canvas.width = rect.width * devicePixelRatio;
      canvas.height = rect.height * devicePixelRatio;
      ctx.setTransform(devicePixelRatio, 0, 0, devicePixelRatio, 0, 0);
      state.width = rect.width;
      state.height = rect.height;
    }

    window.addEventListener("resize", resize, { passive: true });
    resize();

    document.addEventListener("keydown", (e) => {
      keys[e.key.toLowerCase()] = true;
      if (e.key === "1") setMode("surface");
      if (e.key === "2") setMode("underwater");
      if (e.key === " ") {
        pointer.down = true;
        e.preventDefault();
      }
    });

    document.addEventListener("keyup", (e) => {
      keys[e.key.toLowerCase()] = false;
      if (e.key === " ") pointer.down = false;
    });

    canvas.addEventListener("pointermove", (e) => {
      const rect = canvas.getBoundingClientRect();
      pointer.x = e.clientX - rect.left;
      pointer.y = e.clientY - rect.top;
    });

    canvas.addEventListener("pointerdown", () => {
      pointer.down = true;
    });

    canvas.addEventListener("pointerup", () => {
      pointer.down = false;
    });

    canvas.addEventListener("pointerleave", () => {
      pointer.down = false;
    });

    function clamp(value, min, max) {
      return Math.min(Math.max(value, min), max);
    }

    function spawnBubble() {
      state.bubbles.push({
        x: Math.random() * state.width,
        y: state.height + Math.random() * 120,
        r: 4 + Math.random() * 8,
        speed: 12 + Math.random() * 22,
        drift: (Math.random() - 0.5) * 20
      });
    }

    function updateParticles(dt) {
      for (let i = state.sparks.length - 1; i >= 0; i--) {
        const s = state.sparks[i];
        s.life -= dt;
        s.x += s.vx * dt;
        s.y += s.vy * dt;
        s.vy += 180 * dt;
        if (s.life <= 0) state.sparks.splice(i, 1);
      }

      if (state.mode === "underwater") {
        while (state.bubbles.length < 22) spawnBubble();
      } else {
        state.bubbles.length = 0;
      }

      for (let i = state.bubbles.length - 1; i >= 0; i--) {
        const b = state.bubbles[i];
        b.y -= b.speed * dt;
        b.x += b.drift * dt;
        if (b.y < -20 || b.x < -20 || b.x > state.width + 20) {
          state.bubbles.splice(i, 1);
        }
      }
    }

    function updatePlayer(dt) {
      let ax = 0;
      let ay = 0;

      if (keys["a"]) ax -= 1;
      if (keys["d"]) ax += 1;
      if (keys["w"]) ay -= 1;
      if (keys["s"]) ay += 1;

      // Realistic-ish water physics: drag and buoyancy
      const underwater = state.mode === "underwater";
      const acc = underwater ? 210 : 240;

      player.vx += ax * acc * dt;
      player.vy += ay * acc * dt;

      if (underwater) {
        const waterDepth = player.y > state.waterLevel ? 1 : 0.25;
        // Less gravity, more buoyancy below waterline
        player.vy += 90 * dt;
        player.vy -= 280 * waterDepth * dt;
        player.vx += state.current * dt;
        // Fluid drag
        player.vx *= 0.985;
        player.vy *= 0.985;
      } else {
        player.vy += 260 * dt;
        player.vx *= 0.94;
        player.vy *= 0.97;
      }

      const magnitude = Math.hypot(player.vx, player.vy);
      const maxVel = underwater ? 220 : 300;
      if (magnitude > maxVel) {
        const scale = maxVel / magnitude;
        player.vx *= scale;
        player.vy *= scale;
      }

      player.x += player.vx * dt;
      player.y += player.vy * dt;

      player.x = clamp(player.x, 40, state.width - 40);
      player.y = clamp(player.y, 40, state.height - 30);

      if (underwater && player.y > state.waterLevel + 40) {
        player.y = state.waterLevel + 40;
      }
    }

    function nearestPointOnSegment(px, py, sx, sy, ex, ey) {
      const dx = ex - sx;
      const dy = ey - sy;
      const t = clamp(((px - sx) * dx + (py - sy) * dy) / (dx * dx + dy * dy), 0, 1);
      return {
        x: sx + dx * t,
        y: sy + dy * t,
        t
      };
    }

    function updateWelding(dt) {
      const isWelding = pointer.down || keys[" "];
      let totalDone = 0;

      for (const seam of seams) {
        const seamStartX = seam.x;
        const seamEndX = seam.x + seam.length;
        const seamStartY = seam.y;
        const seamEndY = seam.y;

        const torchX = pointer.x || player.x + 22;
        const torchY = pointer.y || player.y + 6;

        const nearest = nearestPointOnSegment(torchX, torchY, seamStartX, seamStartY, seamEndX, seamEndY);
        const dist = Math.hypot(torchX - nearest.x, torchY - nearest.y);

        if (isWelding && dist < 30) {
          const rate = underwater() ? 0.55 : 0.8;
          seam.progress = clamp(seam.progress + rate * dt, 0, 1);
          seam.baseGlow = 1;

          const sparkCount = 2 + Math.random() * 4;
          for (let i = 0; i < sparkCount; i++) {
            state.sparks.push({
              x: torchX,
              y: torchY,
              vx: (Math.random() - 0.5) * 140,
              vy: -40 - Math.random() * 100,
              life: 0.15 + Math.random() * 0.25
            });
          }

          if (state.mode === "underwater") {
            for (let i = 0; i < 2; i++) {
              state.bubbles.push({
                x: torchX + (Math.random() - 0.5) * 18,
                y: torchY + 16,
                r: 3 + Math.random() * 6,
                speed: 18 + Math.random() * 24,
                drift: (Math.random() - 0.5) * 26
              });
            }
          }
        } else {
          seam.baseGlow *= 0.92;
        }

        totalDone += seam.progress;
      }

      state.goal = (totalDone / seams.length) * 100;
      progressText.textContent = `${Math.round(state.goal)}%`;
    }

    function underwater() {
      return state.mode === "underwater";
    }

    function drawBackground() {
      const g = ctx.createLinearGradient(0, 0, 0, state.height);
      g.addColorStop(0, "#24364f");
      g.addColorStop(0.55, "#141d29");
      g.addColorStop(1, "#0d1218");
      ctx.fillStyle = g;
      ctx.fillRect(0, 0, state.width, state.height);

      // Dust / city-like environment
      for (let i = 0; i < 70; i++) {
        const x = (i * 97) % state.width;
        const y = (i * 59) % state.height;
        ctx.fillStyle = "rgba(255,255,255,0.05)";
        ctx.fillRect(x, y, 2, 2);
      }
    }

    function drawWater() {
      if (state.mode !== "underwater") return;

      // Water surface
      const surfaceY = state.waterLevel;
      ctx.fillStyle = "rgba(80, 170, 255, 0.30)";
      ctx.fillRect(0, 0, state.width, surfaceY);

      // Water body
      ctx.fillStyle = "rgba(30, 89, 202, 0.44)";
      ctx.fillRect(0, surfaceY, state.width, state.height - surfaceY);

      // Surface waves
      ctx.beginPath();
      ctx.moveTo(0, surfaceY);
      for (let x = 0; x <= state.width; x += 12) {
        const y = surfaceY + Math.sin((x + state.time * 70) * 0.05) * 7;
        ctx.lineTo(x, y);
      }
      ctx.lineTo(state.width, state.height);
      ctx.lineTo(0, state.height);
      ctx.closePath();
      ctx.fillStyle = "rgba(86, 160, 255, 0.35)";
      ctx.fill();

      // Current lines
      ctx.strokeStyle = "rgba(255,255,255,0.13)";
      ctx.lineWidth = 1.2;
      for (let y = surfaceY + 40; y < state.height; y += 22) {
        ctx.beginPath();
        ctx.moveTo(0, y + Math.sin(state.time * 2 + y * 0.08) * 10);
        for (let x = 0; x <= state.width; x += 22) {
          const yy = y + Math.sin(x * 0.08 + state.time * 2 + y * 0.08) * 10;
          ctx.lineTo(x, yy);
        }
        ctx.stroke();
      }
    }

    function drawPlate() {
      const plateY = 146;
      const plateH = 300;
      const plateX = 120;

      ctx.fillStyle = "#5a616d";
      ctx.fillRect(plateX, plateY, 760, plateH);

      // Steel highlights
      ctx.fillStyle = "rgba(255,255,255,0.13)";
      ctx.fillRect(plateX + 20, plateY + 18, 700, 18);

      ctx.fillStyle = "#2d333b";
      ctx.fillRect(plateX, plateY + plateH - 26, 760, 26);

      // Weld seams
      for (const seam of seams) {
        const seamEndX = seam.x + seam.length;

        // black seam
        ctx.strokeStyle = "#111";
        ctx.lineWidth = 3;
        ctx.beginPath();
        ctx.moveTo(seam.x, seam.y);
        ctx.lineTo(seamEndX, seam.y);
        ctx.stroke();

        // glowing weld bead
        const beadWidth = 8;
        const weldX = seam.x + seam.progress * seam.length;
        const glow = 0.1 + seam.baseGlow * 0.9;

        ctx.fillStyle = `rgba(255, 180, 60, ${0.65 + glow * 0.35})`;
        ctx.fillRect(seam.x, seam.y - beadWidth / 2, weldX - seam.x, beadWidth);

        // bright core
        ctx.fillStyle = "rgba(255, 227, 133, 0.9)";
        ctx.fillRect(seam.x, seam.y - 2, weldX - seam.x, 4);

        // seam completion marker
        ctx.strokeStyle = "rgba(255,255,255,0.18)";
        ctx.beginPath();
        ctx.moveTo(seam.x + seam.progress * seam.length, seam.y - 15);
        ctx.lineTo(seam.x + seam.progress * seam.length, seam.y + 15);
        ctx.stroke();
      }
    }

    function drawTorch() {
      const torchX = pointer.x || player.x + 18;
      const torchY = pointer.y || player.y + 6;

      // Player body
      ctx.fillStyle = "#dce5ef";
      ctx.beginPath();
      ctx.arc(player.x, player.y, player.radius, 0, Math.PI * 2);
      ctx.fill();

      // Helmet / shield
      ctx.fillStyle = "#3a4858";
      ctx.beginPath();
      ctx.arc(player.x, player.y - 4, player.radius * 0.7, 0, Math.PI * 2);
      ctx.fill();

      // Torch arm
      ctx.strokeStyle = "#a6b2bd";
      ctx.lineWidth = 8;
      ctx.beginPath();
      ctx.moveTo(player.x + 12, player.y + 4);
      ctx.lineTo(torchX, torchY);
      ctx.stroke();

      // Torch tip
      ctx.fillStyle = "#1d232d";
      ctx.beginPath();
      ctx.arc(torchX, torchY, 6, 0, Math.PI * 2);
      ctx.fill();

      // Arc flame
      const welding = pointer.down || keys[" "];
      if (welding) {
        const glow = 18 + Math.random() * 20;
        ctx.beginPath();
        ctx.moveTo(torchX, torchY);
        ctx.arc(torchX, torchY - 12, glow, 0.7, 2.5);
        ctx.closePath();
        ctx.fillStyle = "rgba(255, 183, 20, 0.8)";
        ctx.fill();

        ctx.beginPath();
        ctx.moveTo(torchX, torchY - 10);
        ctx.lineTo(torchX + (Math.random() - 0.5) * 18, torchY - 30 - Math.random() * 16);
        ctx.lineTo(torchX + (Math.random() - 0.5) * 16, torchY - 8);
        ctx.closePath();
        ctx.fillStyle = "rgba(255, 88, 30, 0.7)";
        ctx.fill();
      }
    }

    function drawBubbles() {
      for (const b of state.bubbles) {
        ctx.beginPath();
        ctx.arc(b.x, b.y, b.r, 0, Math.PI * 2);
        ctx.fillStyle = "rgba(180, 220, 255, 0.42)";
        ctx.fill();
      }
    }

    function drawSparkParticles() {
      for (const s of state.sparks) {
        ctx.beginPath();
        ctx.arc(s.x, s.y, 2 + s.life * 6, 0, Math.PI * 2);
        ctx.fillStyle = `rgba(255, ${140 + Math.random() * 60}, 60, ${s.life * 2})`;
        ctx.fill();
      }
    }

    function drawHUDLabels() {
      if (state.mode === "underwater") {
        ctx.fillStyle = "rgba(255,255,255,0.75)";
        ctx.font = "16px Arial";
        ctx.fillText("Underwater welding: drag + buoyancy + current", 18, 28);
      } else {
        ctx.fillStyle = "rgba(255,255,255,0.75)";
        ctx.font = "16px Arial";
        ctx.fillText("Surface welding: stronger arc, less resistance", 18, 28);
      }
    }

    function render() {
      drawBackground();
      drawWater();
      drawPlate();
      drawBubbles();
      drawTorch();
      drawSparkParticles();
      drawHUDLabels();
    }

    let last = performance.now();
    function loop(now) {
      const dt = Math.min((now - last) / 1000, 0.033);
      last = now;
      state.time += dt;

      updatePlayer(dt);
      updateWelding(dt);
      updateParticles(dt);

      render();
      requestAnimationFrame(loop);
    }

    setMode("surface");
    requestAnimationFrame(loop);
  </script>
</body>
</html>
