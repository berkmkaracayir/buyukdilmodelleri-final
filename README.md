# Third-Party Breach News Classification — SLM Fine-Tuning Benchmark 🛡️

Bu proje, siber güvenlik ihlal haberlerini **İç İhlal (1)** ve **3. Taraf İhlali / Tedarik Zinciri (2)** olarak sınıflandırmak amacıyla Küçük Dil Modellerinin (SLM) optimize edilerek eğitilmesini (Fine-Tuning) ve kıyaslanmasını içerir.

Proje, kısıtlı donanım kaynaklarında (örn. 15GB VRAM T4 GPU) büyük performans elde etmek üzere Unsloth ve 4-bit QLoRA mimarisiyle tasarlanmıştır.

## 🚀 Özellikler

* **Bellek Optimizasyonu (OOM Koruması):** Unsloth ile 4-bit quantization, dinamik batch sizing ve model arası otomatik VRAM temizliği (Garbage Collection).
* **End-to-End Pipeline:** JSON'dan JSONL formatına veri hazırlama, eğitim, çıkarım (inference) ve görselleştirme tek bir akışta.
* **CTI Perspektifiyle Hata Analizi:** Modellerin başarısız olduğu vakaların Siber Tehdit İstihbaratı (CTI) uzmanı gözüyle incelenmesi.

## 🧠 Karşılaştırılan Modeller

Aşağıdaki modeller 5 epoch, 2e-4 learning rate ve LoRA (r=16, alpha=32) parametreleriyle eğitilmiştir:
1. `HuggingFaceTB/SmolLM2-360M-Instruct`
2. `unsloth/tinyllama-chat-bnb-4bit`
3. `unsloth/qwen2.5-1.5b-instruct-bnb-4bit`
4. `unsloth/gemma-2-2b-it-bnb-4bit`

## ⚙️ Kurulum

Gereksinimleri kurmak için:

```bash
# Standart kütüphanelerin kurulumu
pip install -r requirements.txt

# Unsloth ve bağımlılıklarının kurulumu (CUDA destekli ortamlar için)
pip install "unsloth[colab-new] @ git+[https://github.com/unslothai/unsloth.git](https://github.com/unslothai/unsloth.git)"
pip install --no-deps xformers trl peft accelerate bitsandbytes datasets

## 📊 Benchmark Sonuçları

Test veri seti üzerinde elde edilen nihai performans (Zero-shot sonrası Fine-Tuned):

| Model | Accuracy | Macro-F1 | Weighted-F1 | Çıkarım Hızı (ms/örnek) |
| :--- | :--- | :--- | :--- | :--- |
| **QWEN (1.5B)** | %100.0 | 1.0000 | 1.0000 | 255.4 |
| **TINYLLAMA (1.1B)** | %100.0 | 1.0000 | 1.0000 | 173.8 |
| **SMOLLM2 (360M)** | %75.00 | 0.7402 | 0.7486 | 240.1 |
| **GEMMA 2 (2B)** | %41.67 | 0.2941 | 0.2451 | 490.6 |

*Not: Gemma modelinin binary sınıflandırmada yaşadığı performans kaybı, uzun bağlamlı metinlerdeki dikkat (attention) mekanizmasının padding süreçleriyle ilgili limitasyonlarından kaynaklanmaktadır.*

## 📁 Proje Çıktıları (Outputs)

Çalıştırma sonrasında `outputs/` dizininde şu dosyalar otomatik olarak üretilir:

* `final_results.csv`: Metriklerin dökümü.
* `model_comparison.png`: Performans bar grafiği.
* `efficiency_plot.png`: Çıkarım hızı ve F1 skorunu kıyaslayan verimlilik grafiği.
* `tum_hatalar_analiz.txt`: CTI hata analiz raporu.