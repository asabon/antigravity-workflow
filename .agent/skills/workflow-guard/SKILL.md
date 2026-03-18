# 状態遷移自動算出スキル (workflow-guard)

AI のステータスバー出力に関するケアレスミスを防止し、状態遷移モデル（ACTIVE/IDLEおよび4フェーズ）をカプセル化（一元管理）するためのスキルです。

## 💡 概要

エージェントは、行動の節目（回答の末尾、コマンド完了時など）において、必ずこのスキルに内蔵されたスクリプトを走らせて **「次の正しい状態」** を演算させ、その出力結果をそのままステータスバーとして採用しなければなりません。

## 🛠️ スクリプト構成

### `scripts/calc_next_state.py`

現在の状態・フェーズ、および実行したアクションを基に、遷移先を論理的に算出します。

#### 📥 入力パラメータ (Arguments)

| パラメータ | 必須 | 選択肢 / 例 | 備考 |
| :--- | :--- | :--- | :--- |
| `--status` | ✅ | `ACTIVE` / `IDLE` | 現在の活性状態 |
| `--phase` | ✅ | `PLANNING`, `PREPARING`, `DEVELOPING`, `REVIEWING`, `-` | 現在のフェーズ |
| `--action` | ✅ | `/cleanup`, `/dev-pause`, `issue-create`, `ready`, `kickoff` | 実行したトリガー |
| `--issue` | | `#123` / `-` | 関連 Issue 番号 |
| `--pr` | | `#456` / `-` | 関連 PR 番号 |

#### 💻 使用例 (Usage)

```powershell
python .agent/skills/workflow-guard/scripts/calc_next_state.py --status ACTIVE --phase REVIEWING --action "/cleanup" --issue "-" --pr "-"
```

#### 📤 出力フォーマット (Output)

演算された「次の状態」情報と、そのままコピペして使用できる **ステータスバー文字列** が JSON 構造で返却されます。

---

## ⚠️ 絶対ルール (Guardrail)

エージェントは自らの感覚（アドリブ）で「待機中だから `IDLE`」といった判断をしてはいけません。
**演算スクリプトが算出した文字列のみを真実（SSOT）としてステータスバーに記載すること。**
