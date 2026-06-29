# 🎾 K-Vision Tennis — Computer Vision per l'analisi di match di tennis

> **Case study** di un progetto professionale sviluppato presso **K-Sport World**.
> Il codice sorgente è proprietà dell'azienda: questo repository ne descrive **architettura, approccio e risultati**, non contiene codice.

**Ruolo:** AI / Computer Vision Developer · **Periodo:** Nov 2025 – Apr 2026
**Stack:** Python · PyTorch · YOLOv11 · OpenCV · OCR · Homography · Multi-Object Tracking · NumPy · Pandas

---

## Il problema

Da un video broadcast TV di un match di tennis, ricostruire automaticamente il gioco su **due piani diversi**: *fisico* (dove sono i giocatori e come si muovono sul campo) e *informativo* (qual è il punteggio, chi serve, chi sta giocando), e renderli dati strutturati pronti per l'analisi.

## Architettura a due domini

La scelta progettuale chiave: il problema si decompone in **due domini cognitivi distinti**, tenuti separati in due rami indipendenti che comunicano solo tramite un file "blackboard" con contratti dati espliciti. Così una modifica all'OCR non rischia di rompere il tracking, e viceversa.

| Ramo | Cosa fa |
|------|---------|
| Dominio fisico (CV / GPU) | Segmentazione automatica degli scambi (rally), detection e tracking dei giocatori, court keypoints, omografia, conversione in metri, smoothing, derivazione di velocità e distanze |
| Dominio informativo (OCR) | Lettura del tabellone: punteggio, nomi dei giocatori, chi è al servizio e quale set; matching dei nomi su whitelist |

## I modelli (Deep Learning)

Due modelli YOLO dedicati: **`yolo11l_players`** (giocatori) e **`yolo11m_court`** (keypoints del campo).

Il modello dei giocatori è stato **addestrato da me** con una pipeline completa:

- Dataset costruito da zero: **~5.400 immagini etichettate** dai broadcast dei **4 tornei del Grande Slam** (erba, terra, cemento)
- Pipeline: download → estrazione frame → pre-labeling automatico → correzione → **split stratificato per superficie** → training → **validazione per superficie**
- **Transfer learning** da pesi COCO, **YOLOv11** a `imgsz=1280`

| Metrica | Valore |
|---------|--------|
| mAP@50 | **≈ 0.99** |
| mAP@50-95 | **≈ 0.87** |
| Precision / Recall | **≈ 0.99** |

## Cosa produce

- **Tracce fisiche per giocatore**: posizione sul campo (metri), velocità, distanze percorse
- **Metadati del match** dal ramo informativo: punteggio, nomi, servizio, set
- **Output per match** pronti per l'analisi e la consegna

## Stack

`Python` · `PyTorch` · `YOLOv11` · `OpenCV` · `OCR` · `Homography` · `Multi-Object Tracking` · `NumPy` · `Pandas`

---

*Lavoro professionale svolto presso K-Sport World. Codice riservato; demo dimostrativa disponibile su richiesta.*
