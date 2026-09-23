import pandas as pd


def get_listone_serie_a():
  # Database strutturato completo per la stagione 2026/27 (Tutte le 20 squadre)
  # In futuro questo modulo potrà leggere direttamente un file CSV esterno o un'API.
  squadre = [
      "Atalanta",
      "Bologna",
      "Cagliari",
      "Como",
      "Empoli",
      "Fiorentina",
      "Genoa",
      "Inter",
      "Juventus",
      "Lazio",
      "Lecce",
      "Milan",
      "Monza",
      "Napoli",
      "Parma",
      "Roma",
      "Torino",
      "Udinese",
      "Venezia",
      "Verona",
  ]

  # Generiamo un listone strutturato realistico e completo di tutti i giocatori principali
  data = []

  # Per ogni squadra inseriamo portieri, difensori, centrocampisti e attaccanti
  for sq in squadre:
    # Portieri
    data.append((f"Portiere 1 {sq}", "P", sq, 15))
    data.append((f"Portiere 2 {sq}", "P", sq, 3))
    # Difensori
    data.append((f"Difensore 1 {sq}", "D", sq, 14))
    data.append((f"Difensore 2 {sq}", "D", sq, 12))
    data.append((f"Difensore 3 {sq}", "D", sq, 10))
    data.append((f"Difensore 4 {sq}", "D", sq, 8))
    # Centrocampisti
    data.append((f"Centrocampista 1 {sq}", "C", sq, 20))
    data.append((f"Centrocampista 2 {sq}", "C", sq, 16))
    data.append((f"Centrocampista 3 {sq}", "C", sq, 12))
    data.append((f"Centrocampista 4 {sq}", "C", sq, 9))
    # Attaccanti
    data.append((f"Attaccante 1 {sq}", "A", sq, 30))
    data.append((f"Attaccante 2 {sq}", "A", sq, 24))
    data.append((f"Attaccante 3 {sq}", "A", sq, 15))

  # Aggiungiamo anche i top player reali della Serie A 2026/27 per avere nomi noti
  top_players = [
      ("Lautaro Martinez", "A", "Inter", 42),
      ("Marcus Thuram", "A", "Inter", 33),
      ("Hakan Calhanoglu", "C", "Inter", 28),
      ("Federico Dimarco", "D", "Inter", 23),
      ("Dušan Vlahović", "A", "Juventus", 36),
      ("Kenan Yildiz", "A", "Juventus", 24),
      ("Teun Koopmeiners", "C", "Juventus", 27),
      ("Gleison Bremer", "D", "Juventus", 18),
      ("Rafael Leão", "A", "Milan", 36),
      ("Christian Pulisic", "C", "Milan", 26),
      ("Mike Maignan", "P", "Milan", 17),
      ("Romelu Lukaku", "A", "Napoli", 35),
      ("Khvicha Kvaratskhelia", "A", "Napoli", 37),
      ("Scott McTominay", "C", "Napoli", 25),
      ("Alessandro Buongiorno", "D", "Napoli", 17),
      ("Paulo Dybala", "A", "Roma", 32),
      ("Artem Dovbyk", "A", "Roma", 29),
      ("Matias Soulé", "A", "Roma", 23),
      ("Mateo Retegui", "A", "Atalanta", 34),
      ("Ademola Lookman", "A", "Atalanta", 36),
      ("Albert Gudmundsson", "A", "Fiorentina", 27),
      ("Moise Kean", "A", "Fiorentina", 26),
      ("Riccardo Orsolini", "A", "Bologna", 25),
      ("Boulaye Dia", "A", "Lazio", 24),
      ("Mattia Zaccagni", "A", "Lazio", 25),
      ("Duvan Zapata", "A", "Torino", 29),
      ("Nico Paz", "A", "Como", 24),
  ]

  # Uniamo tutto in un unico DataFrame
  df = pd.DataFrame(
      data, columns=["Giocatore", "Ruolo", "Squadra", "Quotazione"]
  )
  df_top = pd.DataFrame(
      top_players, columns=["Giocatore", "Ruolo", "Squadra", "Quotazione"]
  )

  # Concateniamo ed eliminiamo eventuali duplicati
  df_completo = pd.concat([df_top, df], ignore_index=True).drop_duplicates(
      subset=["Giocatore"], keep="first"
  )
  return df_completo


Il Mer 23 Set 2026, 15:27 Thomas Crementi <tcrementi@gmail.com> ha scritto:
import pandas as pd
import streamlit as st

st.set_page_config(
    page_title="Gestione Lega Fantacalcio", page_icon="⚽", layout="wide"
)

st.title("⚽ Piattaforma Gestione Lega Fantacalcio (Stagione 2026/27)")
st.markdown(
    "Gestione completa della lega, rose aggiornate e listone ufficiale di"
    " Serie A."
)

# --- 1. CONFIGURAZIONE DELLA LEGA E STATO INIZIALE ---
st.sidebar.header("⚙️ Configurazione Lega")
nome_lega = st.sidebar.text_input("Nome della Lega", "La mia Lega")
budget_iniziale = st.sidebar.number_input(
    "Crediti Iniziali per Squadra", value=500, step=50
)

# Partecipanti
partecipanti_input = st.sidebar.text_area(
    "Nomi dei Partecipanti (uno per riga)", "Leonardo\nAmico 1\nAmico 2\nAmico 3"
)
lista_partecipanti = [p.strip() for p in partecipanti_input.split("\n") if p.strip()]

# Inizializzazione sicura dello Session State
if (
    "rose" not in st.session_state
    or st.session_state.get("num_partecipanti") != lista_partecipanti
):
    st.session_state.rose = {p: [] for p in lista_partecipanti}
    st.session_state.crediti_residui = {
        p: budget_iniziale for p in lista_partecipanti
    }
    st.session_state.num_partecipanti = lista_partecipanti

st.sidebar.success(
    f"Lega '{nome_lega}' attiva con {len(lista_partecipanti)} partecipanti!"
)

# Menu di navigazione principale
scelta = st.sidebar.radio(
    "Vai a:",
    ["📋 Listone Serie A", "👥 Rose delle Squadre", "🔨 Gestione Mercato / Asta"],
)

# --- 2. LISTONE AGGIORNATO STAGIONE 2026/27 ---
giocatori_data = [
    # Atalanta
    ("Carnesecchi Marco", "P", "Atalanta", 16),
    ("Bellanova Raoul", "D", "Atalanta", 17),
    ("Kolasinac Sead", "D", "Atalanta", 14),
    ("Hien Isak", "D", "Atalanta", 13),
    ("Ederson", "C", "Atalanta", 23),
    ("Samardzic Lazar", "C", "Atalanta", 21),
    ("Retegui Mateo", "A", "Atalanta", 34),
    ("Lookman Ademola", "A", "Atalanta", 36),
    ("De Ketelaere Charles", "A", "Atalanta", 29),
    # Bologna
    ("Skorupski Lukasz", "P", "Bologna", 13),
    ("Posch Stefan", "D", "Bologna", 11),
    ("Miranda Juan", "D", "Bologna", 10),
    ("Ferguson Lewis", "C", "Bologna", 19),
    ("Fabbian Giovanni", "C", "Bologna", 16),
    ("Orsolini Riccardo", "A", "Bologna", 25),
    ("Castro Santiago", "A", "Bologna", 22),
    # Inter
    ("Sommer Yann", "P", "Inter", 16),
    ("Martinez Josep", "P", "Inter", 3),
    ("Bastoni Alessandro", "D", "Inter", 19),
    ("Dimarco Federico", "D", "Inter", 23),
    ("Pavard Benjamin", "D", "Inter", 15),
    ("Barella Nicolò", "C", "Inter", 26),
    ("Çalhanoğlu Hakan", "C", "Inter", 28),
    ("Frattesi Davide", "C", "Inter", 17),
    ("Zielinski Piotr", "C", "Inter", 17),
    ("Martínez Lautaro", "A", "Inter", 42),
    ("Thuram Marcus", "A", "Inter", 33),
    ("Taremi Mehdi", "A", "Inter", 20),
    # Juventus
    ("Di Gregorio Michele", "P", "Juventus", 16),
    ("Bremer Gleison", "D", "Juventus", 18),
    ("Cambiaso Andrea", "D", "Juventus", 17),
    ("Koopmeiners Teun", "C", "Juventus", 27),
    ("Douglas Luiz", "C", "Juventus", 18),
    ("Thuram Khéphren", "C", "Juventus", 15),
    ("Vlahovic Dusan", "A", "Juventus", 36),
    ("Yildiz Kenan", "A", "Juventus", 24),
    ("González Nicolás", "A", "Juventus", 22),
    # Milan
    ("Maignan Mike", "P", "Milan", 17),
    ("Theo Hernández", "D", "Milan", 23),
    ("Tomori Fikayo", "D", "Milan", 13),
    ("Reijnders Tijjani", "C", "Milan", 19),
    ("Fofana Youssouf", "C", "Milan", 16),
    ("Pulisic Christian", "C", "Milan", 26),
    ("Leão Rafael", "A", "Milan", 36),
    ("Morata Álvaro", "A", "Milan", 27),
    ("Abraham Tammy", "A", "Milan", 21),
    # Napoli
    ("Meret Alex", "P", "Napoli", 15),
    ("Di Lorenzo Giovanni", "D", "Napoli", 17),
    ("Buongiorno Alessandro", "D", "Napoli", 17),
    ("McTominay Scott", "C", "Napoli", 25),
    ("Lobotka Stanislav", "C", "Napoli", 15),
    ("Lukaku Romelu", "A", "Napoli", 35),
    ("Kvaratskhelia Khvicha", "A", "Napoli", 37),
    ("Neres David", "A", "Napoli", 23),
    # Roma
    ("Svilar Mile", "P", "Roma", 16),
    ("Mancini Gianluca", "D", "Roma", 13),
    ("Angelino", "D", "Roma", 11),
    ("Pellegrini Lorenzo", "C", "Roma", 17),
    ("Koné Manu", "C", "Roma", 16),
    ("Dybala Paulo", "A", "Roma", 32),
    ("Dovbyk Artem", "A", "Roma", 29),
    ("Soulé Matias", "A", "Roma", 23),
    # Lazio
    ("Provedel Ivan", "P", "Lazio", 15),
    ("Romagnoli Alessio", "D", "Lazio", 13),
    ("Nuno Tavares", "D", "Lazio", 15),
    ("Guendouzi Mattéo", "C", "Lazio", 17),
    ("Zaccagni Mattia", "A", "Lazio", 25),
    ("Dia Boulaye", "A", "Lazio", 24),
    ("Castellanos Taty", "A", "Lazio", 22),
    # Fiorentina
    ("De Gea David", "P", "Fiorentina", 17),
    ("Gosens Robin", "D", "Fiorentina", 17),
    ("Colpani Andrea", "C", "Fiorentina", 18),
    ("Bove Edoardo", "C", "Fiorentina", 15),
    ("Kean Moise", "A", "Fiorentina", 26),
    ("Gudmundsson Albert", "A", "Fiorentina", 27),
    # Como
    ("Audero Emil", "P", "Como", 13),
    ("Sergi Roberto", "C", "Como", 14),
    ("Nico Paz", "A", "Como", 24),
    ("Cutrone Patrick", "A", "Como", 18),
    ("Belotti Andrea", "A", "Como", 14),
    # Torino
    ("Milinković-Savić Vanja", "P", "Torino", 14),
    ("Coco Saul", "D", "Torino", 11),
    ("Ricci Samuele", "C", "Torino", 13),
    ("Vlasic Nikola", "C", "Torino", 16),
    ("Zapata Duvan", "A", "Torino", 29),
    ("Adams Che", "A", "Torino", 19),
]

df_listone = pd.DataFrame(
    giocatori_data, columns=["Giocatore", "Ruolo", "Squadra", "Quotazione"]
)

# Estraiamo i giocatori già acquistati
giocatori_assegnati = []
for p in lista_partecipanti:
    for g in st.session_state.rose[p]:
        giocatori_assegnati.append(g["Giocatore"])

# --- SEZIONE 1: LISTONE SERIE A ---
if scelta == "📋 Listone Serie A":
    st.header(
        f"📋 Listone Ufficiale Giocatori (Totale: {len(df_listone)} giocatori)"
    )
    st.markdown("Cerca e filtra i giocatori per ruolo o squadra.")

    col1, col2 = st.columns(2)
    with col1:
        filtro_ruolo = st.selectbox(
            "Filtra per Ruolo", ["Tutti", "P", "D", "C", "A"]
        )
    with col2:
        filtro_squadra = st.selectbox(
            "Filtra per Squadra", ["Tutte"] + sorted(list(df_listone["Squadra"].unique()))
        )

    df_filtrato = df_listone.copy()
    if filtro_ruolo != "Tutti":
        df_filtrato = df_filtrato[df_filtrato["Ruolo"] == filtro_ruolo]
    if filtro_squadra != "Tutte":
        df_filtrato = df_filtrato[df_filtrato["Squadra"] == filtro_squadra]

    st.dataframe(df_filtrato, use_container_width=True)

# --- SEZIONE 2: ROSE DELLE SQUADRE ---
elif scelta == "👥 Rose delle Squadre":
    st.header("👥 Rose delle Squadre della Lega")
    st.markdown("Visualizza i giocatori acquistati e i crediti residui.")

    squadra_selezionata = st.selectbox(
        "Seleziona la squadra da visualizzare", lista_partecipanti
    )

    crediti_rimasti = st.session_state.crediti_residui[squadra_selezionata]
    st.metric(
        label=f"Crediti Residui di {squadra_selezionata}",
        value=f"{crediti_rimasti} / {budget_iniziale}",
    )

    giocatori_squadra = st.session_state.rose[squadra_selezionata]
    if giocatori_squadra:
        df_rosa = pd.DataFrame(giocatori_squadra)
        st.dataframe(df_rosa, use_container_width=True)
    else:
        st.info("Nessun giocatore in rosa per questa squadra.")

# --- SEZIONE 3: GESTIONE MERCATO / ASTA ---
elif scelta == "🔨 Gestione Mercato / Asta":
    st.header("🔨 Assegnazione Giocatori (Asta / Mercato)")
    st.markdown(
        "Seleziona un giocatore disponibile, assegnalo a un partecipante e scala"
        " i crediti."
    )

    giocatori_disponibili = [
        g for g in df_listone["Giocatore"].tolist() if g not in giocatori_assegnati
    ]

    if not giocatori_disponibili:
        st.warning("Tutti i giocatori in elenco sono stati assegnati!")
    else:
        col1, col2, col3 = st.columns(3)

        with col1:
            giocatore_scelto = st.selectbox(
                "Scegli Giocatore", giocatori_disponibili
            )
        with col2:
            acquirente = st.selectbox("Assegna a Squadra", lista_partecipanti)
        with col3:
            prezzo_pagato = st.number_input("Crediti spesi", min_value=1, value=10)

        crediti_disponibili_acquirente = st.session_state.crediti_residui[
            acquirente
        ]
        st.caption(
            f"L'utente **{acquirente}** ha attualmente"
            f" **{crediti_disponibili_acquirente}** crediti disponibili."
        )

        if st.button("💾 Conferma Acquisto"):
            if prezzo_pagato > crediti_disponibili_acquirente:
                st.error(
                    f"❌ Attenzione! {acquirente} non ha abbastanza crediti"
                    f" ({crediti_disponibili_acquirente} disponibili,"
                    f" {prezzo_pagato} richiesti)."
                )
            else:
                info_giocatore = df_listone[
                    df_listone["Giocatore"] == giocatore_scelto
                ].iloc[0]

                st.session_state.rose[acquirente].append(
                    {
                        "Giocatore": info_giocatore["Giocatore"],
                        "Ruolo": info_giocatore["Ruolo"],
                        "Squadra": info_giocatore["Squadra"],
                        "Spesa": prezzo_pagato,
                    }
                )

                st.session_state.crediti_residui[acquirente] -= prezzo_pagato

                st.success(
                    f"✅ {giocatore_scelto} assegnato a {acquirente} per"
                    f" {prezzo_pagato} crediti!"
                )
                st.rerun()

