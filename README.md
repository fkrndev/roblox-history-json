# Roblox History JSON Schema

File-file ini harus di-push ke repo GitHub: `https://github.com/fkrndev/roblox-history-json`

## Struktur Repo

```
roblox-history-json/
├── manifest.json          # Daftar semua scenario (WAJIB)
├── hattin-1187.json       # File scenario individual
├── ain-jalut-1260.json
└── ...                    # Tambah scenario baru sesuka hati
```

## manifest.json

```json
{
  "scenarios": [
    {
      "id": "hattin-1187",
      "title": "Pertempuran Hattin",
      "file": "hattin-1187.json",
      "year": 1187,
      "description": "Deskripsi singkat"
    }
  ]
}
```

## Scenario JSON

```json
{
  "id": "hattin-1187",
  "title": "Pertempuran Hattin",
  "year": 1187,
  "description": "Deskripsi panjang scenario",
  "durationDays": 2,
  "mapSize": 400,
  "landmarks": [
    {
      "name": "Tanduk Hattin",
      "x": 0,
      "z": 0,
      "type": "mountain"
    }
  ],
  "factions": [
    {
      "id": "ayyubid",
      "name": "Dinasti Ayyubiyah",
      "color": "#1a6b1a"
    }
  ],
  "units": [
    {
      "id": "saladin",
      "name": "Salahuddin Al-Ayyubi",
      "faction": "ayyubid",
      "role": "commander",
      "path": [
        {
          "day": 0,
          "hour": 6,
          "x": -150,
          "z": -150,
          "event": "Deskripsi event saat waypoint ini (opsional)"
        },
        {
          "day": 0,
          "hour": 12,
          "x": -50,
          "z": -50
        }
      ]
    }
  ],
  "events": [
    {
      "day": 0,
      "hour": 10,
      "title": "Judul Event",
      "description": "Deskripsi event",
      "factionId": "ayyubid"
    }
  ]
}
```

## Field Reference

### Scenario Root
| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|------------|
| `id` | string | ya | ID unik scenario |
| `title` | string | ya | Judul yang tampil di GUI |
| `year` | number | ya | Tahun Masehi |
| `description` | string | tidak | Deskripsi scenario |
| `durationDays` | number | tidak | Total hari simulasi (default: 1) |
| `mapSize` | number | tidak | Ukuran baseplate dalam studs (default: 400) |
| `landmarks` | array | tidak | Landmark di map (gunung, kota, sungai) |
| `factions` | array | ya | Daftar faksi yang bertikai |
| `units` | array | ya | Daftar tokoh/pasukan dengan path gerakan |
| `events` | array | tidak | Event global yang muncul di event log |

### Landmark
| Field | Tipe | Keterangan |
|-------|------|------------|
| `name` | string | Nama landmark (tampil di map) |
| `x` | number | Posisi X di map (studs) |
| `z` | number | Posisi Z di map (studs) |
| `type` | string | `mountain`, `city`, `river`, atau `marker` (default) |

### Faction
| Field | Tipe | Keterangan |
|-------|------|------------|
| `id` | string | ID unik faksi (dipakai di unit.faction) |
| `name` | string | Nama faksi (tampil di GUI + di atas NPC) |
| `color` | string | Hex color `#rrggbb` untuk warna NPC |

### Unit
| Field | Tipe | Keterangan |
|-------|------|------------|
| `id` | string | ID unik unit |
| `name` | string | Nama tokoh (tampil di atas NPC) |
| `faction` | string | ID faksi (harus match dengan factions[].id) |
| `role` | string | `commander`/`king`/`leader` = NPC lebih besar + highlight emas. Lainnya = soldier biasa |
| `path` | array | Array waypoint gerakan (urutan waktu) |

### Waypoint (dalam path)
| Field | Tipe | Keterangan |
|-------|------|------------|
| `day` | number | Hari ke- (mulai dari 0) |
| `hour` | number | Jam (0-23) |
| `minute` | number | Menit (0-59, opsional) |
| `x` | number | Posisi X di map (studs) |
| `z` | number | Posisi Z di map (studs) |
| `event` | string | Event yang muncul saat unit mencapai waypoint ini (opsional) |

### Event (dalam events[])
| Field | Tipe | Keterangan |
|-------|------|------------|
| `day` | number | Hari ke- |
| `hour` | number | Jam (0-23) |
| `minute` | number | Menit (opsional) |
| `title` | string | Judul event (tampil di event log) |
| `description` | string | Deskripsi event |
| `factionId` | string | ID faksi terkait (opsional) |

## Sistem Koordinat

- Map berpusat di origin (0, 0)
- `x` = sumbu X (positif = timur, negatif = barat)
- `z` = sumbu Z (positif = selatan, negatif = utara)
- Skala: 1 unit = 1 stud di Roblox
- Map default 400x400 studs (dari -200 sampai +200)

## Cara Kerja Simulasi

1. Game fetch `manifest.json` dari GitHub untuk list scenario
2. User klik scenario → game fetch file JSON scenario
3. Map dibangun (baseplate + grid + landmarks)
4. NPC di-spawn untuk setiap unit di posisi waypoint pertama
5. User tekan Play → waktu simulasi maju
6. NPC bergerak mengikuti path, interpolasi antar waypoint
7. Event muncul di event log saat waktu tercapai
8. User bisa pause, ubah kecepatan, atau seek ke waktu tertentu

## Kecepatan Simulasi

- 10x = 10 detik sim per detik real
- 1m/s = 60 detik sim per detik real (1 menit sim per detik)
- 5m/s = 300 detik sim per detik real
- 10m/s = 600 detik sim per detik real
- 30m/s = 1800 detik sim per detik real
