Check out the raw [HTML Dashboard](/biochemistry-flash-cards.html).

<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Biochemistry Flash Cards</title>
  <style>
    :root {
      color-scheme: light dark;
      --bg: #f5f7fb; --surface: #fff; --text: #172033; --muted: #667085;
      --border: #d6dce8; --accent: #3157d5; --accentText: #fff;
      --good: #147a4b; --goodBg: #eaf8f0; --bad: #b42318; --badBg: #fff0ee;
      --shadow: 0 12px 35px rgba(31, 42, 68, .12);
    }
    @media (prefers-color-scheme: dark) {
      :root {
        --bg: #101522; --surface: #1a2130; --text: #eef2f8; --muted: #aab4c7;
        --border: #374154; --accent: #7d9aff; --accentText: #101522;
        --good: #68d9a1; --goodBg: #153529; --bad: #ff9188; --badBg: #3d211f;
        --shadow: 0 12px 35px rgba(0, 0, 0, .3);
      }
    }
    * { box-sizing: border-box; }
    body {
      margin: 0; min-height: 100vh; background: var(--bg); color: var(--text);
      font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
    }
    main { width: min(760px, 100%); margin: 0 auto; padding: 24px 16px 40px; }
    h1 { margin: 0; font-size: clamp(1.55rem, 5vw, 2.1rem); }
    .subtitle { margin: 6px 0 20px; color: var(--muted); }
    .stats { display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px; margin-bottom: 14px; }
    .stat { background: var(--surface); border: 1px solid var(--border); border-radius: 12px; padding: 10px; text-align: center; }
    .stat strong { display: block; font-size: 1.2rem; }
    .stat span { color: var(--muted); font-size: .82rem; }
    .toolbar, .actions, .nav { display: flex; flex-wrap: wrap; gap: 10px; }
    .toolbar { margin-bottom: 14px; }
    button, select {
      min-height: 44px; border: 1px solid var(--border); border-radius: 10px;
      background: var(--surface); color: var(--text); padding: 10px 14px; font: inherit;
    }
    button { cursor: pointer; font-weight: 650; }
    button:hover { filter: brightness(.97); }
    button:focus-visible, select:focus-visible { outline: 3px solid color-mix(in srgb, var(--accent) 40%, transparent); outline-offset: 2px; }
    .primary { background: var(--accent); color: var(--accentText); border-color: var(--accent); }
    .card {
      min-height: 330px; background: var(--surface); border: 1px solid var(--border);
      border-radius: 18px; box-shadow: var(--shadow); padding: clamp(22px, 5vw, 40px);
      display: flex; flex-direction: column; justify-content: center; text-align: center;
      cursor: pointer; user-select: none;
    }
    .label { color: var(--accent); font-size: .78rem; font-weight: 800; letter-spacing: .12em; text-transform: uppercase; }
    .question { font-size: clamp(1.2rem, 4.5vw, 1.65rem); line-height: 1.4; margin: 16px 0; }
    .answer { border-top: 1px solid var(--border); margin-top: 12px; padding-top: 22px; font-size: 1.1rem; line-height: 1.55; }
    .hint { color: var(--muted); font-size: .88rem; margin-top: 18px; }
    .actions { justify-content: center; margin: 16px 0; }
    .correct { color: var(--good); background: var(--goodBg); border-color: var(--good); }
    .wrong { color: var(--bad); background: var(--badBg); border-color: var(--bad); }
    .nav { justify-content: space-between; }
    .nav button { flex: 1; }
    .progress { height: 8px; background: var(--border); border-radius: 999px; overflow: hidden; margin: 16px 0 8px; }
    .progress > div { height: 100%; background: var(--accent); transition: width .2s ease; }
    .status { text-align: center; color: var(--muted); font-size: .9rem; min-height: 1.4em; }
    details { margin-top: 22px; color: var(--muted); }
    details button { margin-top: 10px; color: var(--bad); }
    [hidden] { display: none !important; }
    @media (max-width: 430px) {
      main { padding-top: 16px; }
      .card { min-height: 300px; }
      .toolbar > * { flex: 1 1 45%; }
    }
  </style>
</head>
<body>
  <main>
    <h1>Biochemistry Flash Cards</h1>
    <p class="subtitle">Reveal the answer, then mark how you did.</p>

    <section class="stats" aria-label="Study statistics">
      <div class="stat"><strong id="rightCount">0</strong><span>Right</span></div>
      <div class="stat"><strong id="wrongCount">0</strong><span>Wrong</span></div>
      <div class="stat"><strong id="mastery">—</strong><span>Accuracy</span></div>
    </section>

    <div class="toolbar">
      <label>
        <span class="label">Study set</span><br>
        <select id="mode" aria-label="Choose study set">
          <option value="all">All cards</option>
          <option value="missed">Needs practice</option>
          <option value="unseen">Not answered yet</option>
        </select>
      </label>
      <button id="shuffle" type="button">Shuffle cards</button>
    </div>

    <div class="progress" role="progressbar" aria-label="Deck progress" aria-valuemin="0" aria-valuemax="100" aria-valuenow="0"><div id="progressBar"></div></div>
    <div id="position" class="status" aria-live="polite"></div>

    <article id="card" class="card" role="button" aria-label="Flash card. Activate to reveal the answer">
      <div class="label" id="sideLabel">Question</div>
      <div id="question" class="question"></div>
      <div id="answer" class="answer" hidden></div>
      <div id="hint" class="hint">Tap the card or press Space to reveal the answer</div>
    </article>

    <div id="gradeActions" class="actions" hidden>
      <button id="wrong" class="wrong" type="button">✕ I got it wrong</button>
      <button id="correct" class="correct" type="button">✓ I got it right</button>
    </div>
    <div class="nav">
      <button id="previous" type="button">← Previous</button>
      <button id="reveal" class="primary" type="button">Show answer</button>
      <button id="next" type="button">Next →</button>
    </div>

    <details>
      <summary>Keyboard shortcuts and progress</summary>
      <p><strong>Space:</strong> reveal answer · <strong>1:</strong> wrong · <strong>2:</strong> right · <strong>←/→:</strong> previous/next.</p>
      <p>Your right/wrong history is saved automatically in this browser. Use “Needs practice” to study cards whose latest result was wrong.</p>
      <button id="reset" type="button">Reset all progress</button>
    </details>
  </main>

  <script>
    const cards = [
      {q:"What do all organic compounds have in common?", a:"They contain carbon bonded to hydrogen. Carbon atoms can form chains and rings that make many complex biological molecules."},
      {q:"Which macromolecule includes starch, glucose, and cellulose?", a:"Carbohydrates."},
      {q:"Which macromolecule includes fatty acids, phospholipids, and cholesterol?", a:"Lipids."},
      {q:"Which macromolecule includes enzymes, hemoglobin, antibodies, and hair?", a:"Proteins."},
      {q:"Which macromolecule includes DNA, RNA, and ATP?", a:"Nucleic acids (ATP is a nucleotide)."},
      {q:"What is the main function of carbohydrates?", a:"They are a short-term or readily available energy source. Some carbohydrates also provide structure, such as cellulose in plant cell walls."},
      {q:"What is the main function of lipids?", a:"Long-term energy storage. Lipids also form cell membranes, provide insulation, and include some hormones."},
      {q:"What are major functions of proteins?", a:"Structure, enzymes, antibodies, transport, and some hormones."},
      {q:"What is the main function of nucleic acids?", a:"They store and transmit hereditary (genetic) information. DNA and RNA provide instructions for making proteins."},
      {q:"What is the monomer (subunit) of a carbohydrate?", a:"A monosaccharide, or simple sugar, such as glucose."},
      {q:"What are the main building blocks of lipids?", a:"Glycerol and fatty acids. Lipids are not true polymers, but these smaller components build many lipids."},
      {q:"What is the monomer (subunit) of a protein?", a:"An amino acid."},
      {q:"What is the monomer (subunit) of a nucleic acid?", a:"A nucleotide."},
      {q:"How are monomers and polymers related?", a:"A monomer is a small building-block molecule. A polymer is a large molecule made by linking many repeating monomers."},
      {q:"How are catalysts and enzymes related?", a:"A catalyst speeds up a chemical reaction without being used up. An enzyme is a biological catalyst, usually a protein."},
      {q:"What are the enzyme, substrate, and active site?", a:"The enzyme is the catalyst. The substrate is the reactant it acts on. The active site is the specially shaped region where the substrate binds."},
      {q:"What is activation energy? Give an example.", a:"Activation energy is the minimum energy needed to start a reaction. For example, a match needs heat from friction before it begins to burn."},
      {q:"What does an enzyme do?", a:"It lowers a reaction’s activation energy, making the reaction happen faster, while the enzyme itself is not consumed."},
      {q:"What happens when a large temperature or pH change alters an enzyme’s shape?", a:"The enzyme can denature. Its active site changes shape, so the substrate may no longer fit and the reaction slows or stops."},
      {q:"What is a polar covalent bond?", a:"Electrons are shared unequally, producing partial positive and partial negative poles."},
      {q:"What is a nonpolar covalent bond?", a:"Electrons are shared equally, so no partial positive and negative poles form."},
      {q:"What is a hydrogen bond?", a:"A weak attraction between a partially positive hydrogen on one polar molecule and a partially negative region of another polar molecule."},
      {q:"Where are the positive and negative poles in a water molecule?", a:"The two hydrogen ends are partially positive (δ+), and the oxygen end is partially negative (δ−)."},
      {q:"Why don’t oil and water mix?", a:"Water is polar, while the many bonds in oil are nonpolar. Polar water molecules attract one another and exclude the nonpolar oil—“like dissolves like.”"},
      {q:"In sugar dissolved in coffee, identify the solution, solvent, and solute.", a:"Solution: sweetened coffee. Solvent: the mostly water-based coffee that does the dissolving. Solute: the sugar that is dissolved."},
      {q:"How do adhesion and cohesion differ?", a:"Cohesion is attraction between molecules of the same substance, such as water to water. Adhesion is attraction between different substances, such as water to glass."},
      {q:"What values on the pH scale are acidic, neutral, and basic?", a:"Acidic: below 7. Neutral: 7. Basic (alkaline): above 7. On the review sheet, color acid red, neutral yellow, and base blue."},
      {q:"What does water’s high specific heat mean?", a:"Water can absorb or release a lot of heat with only a small temperature change, so its temperature changes slowly."},
      {q:"How do metabolism and homeostasis differ?", a:"Metabolism is the total of all chemical reactions in an organism. Homeostasis is the maintenance of stable internal conditions."},
      {q:"What is an independent variable?", a:"The factor deliberately changed or manipulated by the experimenter."},
      {q:"What is a dependent variable?", a:"The factor measured or observed in response to the independent variable."},
      {q:"What is a control in an experiment?", a:"A comparison group or standard that does not receive the tested treatment, helping show whether the independent variable caused the result."},
      {q:"What is a constant in an experiment?", a:"A factor kept the same for all groups and trials so it does not affect the results."}
    ];

    // Version 2 starts a fresh progress record while preserving future sessions.
    const storageKey = "biochemistry-flashcards-v2";
    localStorage.removeItem("biochemistry-flashcards-v1");
    let results = {};
    try { results = JSON.parse(localStorage.getItem(storageKey)) || {}; } catch (_) {}
    let deck = cards.map((_, i) => i), index = 0, revealed = false;
    const $ = id => document.getElementById(id);

    function filteredDeck() {
      const mode = $("mode").value;
      if (mode === "missed") return cards.map((_,i)=>i).filter(i => results[i]?.last === "wrong");
      if (mode === "unseen") return cards.map((_,i)=>i).filter(i => !results[i]);
      return cards.map((_,i)=>i);
    }

    function save() { localStorage.setItem(storageKey, JSON.stringify(results)); }
    function updateStats() {
      const right = Object.values(results).reduce((n,r)=>n+(r.right||0),0);
      const wrong = Object.values(results).reduce((n,r)=>n+(r.wrong||0),0);
      $("rightCount").textContent = right;
      $("wrongCount").textContent = wrong;
      $("mastery").textContent = right + wrong ? Math.round(right/(right+wrong)*100)+"%" : "—";
    }
    function render() {
      if (!deck.length) {
        $("question").textContent = $("mode").value === "missed" ? "Nothing needs practice yet." : "You have answered every card.";
        $("answer").hidden = true; $("hint").textContent = "Choose another study set to continue.";
        $("gradeActions").hidden = true; $("reveal").disabled = true;
        $("previous").disabled = true; $("next").disabled = true;
        $("position").textContent = "0 cards"; $("progressBar").style.width = "0%";
        return;
      }
      index = (index + deck.length) % deck.length;
      const cardIndex = deck[index], item = cards[cardIndex], record = results[cardIndex];
      revealed = false; $("question").textContent = item.q; $("answer").textContent = item.a;
      $("answer").hidden = true; $("sideLabel").textContent = "Question";
      $("hint").textContent = record ? `Last result: ${record.last === "right" ? "Right" : "Needs practice"}` : "Tap the card or press Space to reveal the answer";
      $("gradeActions").hidden = true; $("reveal").hidden = false; $("reveal").disabled = false;
      $("previous").disabled = false; $("next").disabled = false;
      $("position").textContent = `Card ${index+1} of ${deck.length}`;
      const pct = Math.round((index+1)/deck.length*100);
      $("progressBar").style.width = pct+"%";
      document.querySelector(".progress").setAttribute("aria-valuenow", pct);
    }
    function reveal() {
      if (!deck.length || revealed) return;
      revealed = true; $("answer").hidden = false; $("sideLabel").textContent = "Answer";
      $("hint").textContent = "How did you do?"; $("gradeActions").hidden = false; $("reveal").hidden = true;
    }
    function grade(mark) {
      if (!deck.length || !revealed) return;
      const cardIndex = deck[index]; results[cardIndex] ||= {right:0, wrong:0};
      results[cardIndex][mark]++; results[cardIndex].last = mark; save(); updateStats();
      if ($("mode").value !== "all") {
        const current = cardIndex; deck = filteredDeck();
        const located = deck.indexOf(current); index = located >= 0 ? located + 1 : Math.min(index, deck.length-1);
      } else index++;
      render();
    }
    function move(amount) { if (deck.length) { index += amount; render(); } }
    $("card").addEventListener("click", reveal); $("reveal").addEventListener("click", reveal);
    $("correct").addEventListener("click", ()=>grade("right")); $("wrong").addEventListener("click", ()=>grade("wrong"));
    $("next").addEventListener("click", ()=>move(1)); $("previous").addEventListener("click", ()=>move(-1));
    $("mode").addEventListener("change", ()=>{ deck=filteredDeck(); index=0; render(); });
    $("shuffle").addEventListener("click", ()=>{ for(let i=deck.length-1;i>0;i--){const j=Math.floor(Math.random()*(i+1));[deck[i],deck[j]]=[deck[j],deck[i]];} index=0; render(); });
    $("reset").addEventListener("click", ()=>{ if(confirm("Reset all right/wrong history?")){results={};save();deck=filteredDeck();index=0;updateStats();render();} });
    document.addEventListener("keydown", e=>{
      if (["SELECT","BUTTON"].includes(document.activeElement.tagName)) return;
      if (e.code === "Space") { e.preventDefault(); reveal(); }
      else if (e.key === "1") grade("wrong"); else if (e.key === "2") grade("right");
      else if (e.key === "ArrowLeft") move(-1); else if (e.key === "ArrowRight") move(1);
    });
    updateStats(); render();
  </script>
</body>
</html>
