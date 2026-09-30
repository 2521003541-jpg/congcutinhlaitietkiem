import streamlit as st
import pandas as pd

# =========================
# CẤU HÌNH TRANG
# =========================
st.set_page_config(
    page_title="Tính lãi tiết kiệm",
    page_icon="💰",
    layout="centered"
)

# =========================
# CSS
# =========================
st.markdown("""
<style>
    .main-title {
        text-align: center;
        font-size: 32px;
        font-weight: bold;
        margin-bottom: 5px;
    }

    .sub-title {
        text-align: center;
        color: #666;
        margin-bottom: 25px;
    }

    .result-box {
        padding: 18px;
        border-radius: 10px;
        background-color: #f5f7fa;
        margin-bottom: 10px;
    }

    .result-label {
        font-size: 15px;
        color: #555;
    }

    .result-value {
        font-size: 23px;
        font-weight: bold;
    }
</style>
""", unsafe_allow_html=True)

# =========================
# TIÊU ĐỀ
# =========================
st.markdown(
    '<div class="main-title">💰 TÍNH LÃI TIỀN GỬI TIẾT KIỆM</div>',
    unsafe_allow_html=True
)

st.markdown(
    '<div class="sub-title">Tính lãi đơn và lãi kép theo kỳ hạn gửi</div>',
    unsafe_allow_html=True
)

# =========================
# NHẬP THÔNG TIN
# =========================
st.header("📋 Thông tin khoản tiền gửi")

col1, col2 = st.columns(2)

with col1:
    tien_gui = st.number_input(
        "Số tiền gửi (VNĐ)",
        min_value=0.0,
        value=10000000.0,
        step=100000.0,
        format="%.0f"
    )

    ky_han = st.number_input(
        "Kỳ hạn (tháng)",
        min_value=1,
        max_value=1200,
        value=12,
        step=1
    )

with col2:
    lai_suat = st.number_input(
        "Lãi suất (%/năm)",
        min_value=0.0,
        max_value=100.0,
        value=6.0,
        step=0.1
    )

    loai_lai = st.selectbox(
        "Hình thức tính lãi",
        [
            "Lãi đơn",
            "Lãi kép"
        ]
    )

# =========================
# HÌNH THỨC NHẬN LÃI
# =========================
hinh_thuc_nhan_lai = st.selectbox(
    "Hình thức nhận lãi",
    [
        "Lãnh lãi hàng tháng",
        "Lãnh lãi hàng quý",
        "Lãnh lãi cuối kỳ"
    ]
)

st.divider()

# =========================
# NÚT TÍNH
# =========================
if st.button("🧮 TÍNH TIỀN LÃI", use_container_width=True):

    if tien_gui <= 0:
        st.error("Vui lòng nhập số tiền gửi lớn hơn 0.")
        st.stop()

    if lai_suat < 0:
        st.error("Lãi suất không được nhỏ hơn 0.")
        st.stop()

    # Lãi suất dạng thập phân
    lai_suat_nam = lai_suat / 100

    # Số tháng thực tế
    so_thang = int(ky_han)

    # ==========================================
    # XÁC ĐỊNH SỐ KỲ NHẬN LÃI
    # ==========================================
    if hinh_thuc_nhan_lai == "Lãnh lãi hàng tháng":
        so_ky = so_thang
        so_thang_moi_ky = 1
        ten_ky = "Tháng"

    elif hinh_thuc_nhan_lai == "Lãnh lãi hàng quý":
        so_ky = so_thang // 3
        so_thang_moi_ky = 3
        ten_ky = "Quý"

    else:
        so_ky = 1
        so_thang_moi_ky = so_thang
        ten_ky = "Cuối kỳ"

    # ==========================================
    # TRƯỜNG HỢP LÃI ĐƠN
    # ==========================================
    if loai_lai == "Lãi đơn":

        # Tổng lãi:
        # P * r * t
        tong_lai = tien_gui * lai_suat_nam * (so_thang / 12)

        # Lãi mỗi kỳ
        lai_moi_ky = tong_lai / so_ky

        tong_tien = tien_gui + tong_lai

        # Tạo bảng chi tiết
        data = []

        for i in range(1, so_ky + 1):
            data.append({
                "Kỳ": f"{ten_ky} {i}",
                "Tiền gốc": tien_gui,
                "Tiền lãi kỳ này": lai_moi_ky,
                "Tổng tiền": tien_gui + lai_moi_ky
            })

    # ==========================================
    # TRƯỜNG HỢP LÃI KÉP
    # ==========================================
    else:

        # Số lần ghép lãi trong một năm
        if hinh_thuc_nhan_lai == "Lãnh lãi hàng tháng":
            n = 12
            so_ky = so_thang

        elif hinh_thuc_nhan_lai == "Lãnh lãi hàng quý":
            n = 4
            so_ky = so_thang // 3

        else:
            # Cuối kỳ:
            # Nếu chỉ nhận lãi cuối kỳ thì tính ghép
            # theo năm; với kỳ hạn dưới 1 năm,
            # dùng số kỳ theo tháng để tính.
            n = 12
            so_ky = 1

        # --------------------------------------
        # Lãi kép nhận hàng tháng
        # --------------------------------------
        if hinh_thuc_nhan_lai == "Lãnh lãi hàng tháng":

            lai_moi_ky = None
            so_du = tien_gui
            data = []

            for i in range(1, so_ky + 1):

                lai_ky = so_du * (lai_suat_nam / 12)
                so_du += lai_ky

                data.append({
                    "Kỳ": f"Tháng {i}",
                    "Tiền gốc đầu kỳ": so_du - lai_ky,
                    "Tiền lãi kỳ này": lai_ky,
                    "Tổng tiền": so_du
                })

            tong_tien = so_du
            tong_lai = tong_tien - tien_gui

            # Trong trường hợp lãi kép,
            # tiền lãi kỳ cuối được hiển thị
            # là lãi của kỳ cuối.
            lai_moi_ky = data[-1]["Tiền lãi kỳ này"]

        # --------------------------------------
        # Lãi kép nhận hàng quý
        # --------------------------------------
        elif hinh_thuc_nhan_lai == "Lãnh lãi hàng quý":

            so_du = tien_gui
            data = []

            for i in range(1, so_ky + 1):

                lai_ky = so_du * (lai_suat_nam / 4)
                so_du += lai_ky

                data.append({
                    "Kỳ": f"Quý {i}",
                    "Tiền gốc đầu kỳ": so_du - lai_ky,
                    "Tiền lãi kỳ này": lai_ky,
                    "Tổng tiền": so_du
                })

            tong_tien = so_du
            tong_lai = tong_tien - tien_gui
            lai_moi_ky = data[-1]["Tiền lãi kỳ này"]

        # --------------------------------------
        # Lãi kép nhận cuối kỳ
        # --------------------------------------
        else:

            # Ghép lãi theo tháng để phù hợp
            # với kỳ hạn nhập theo tháng
            so_du = tien_gui
            data = []

            for thang in range(1, so_thang + 1):

                lai_thang = so_du * (lai_suat_nam / 12)
                so_du += lai_thang

            tong_tien = so_du
            tong_lai = tong_tien - tien_gui
            lai_moi_ky = tong_lai

            data.append({
                "Kỳ": "Cuối kỳ",
                "Tiền gốc đầu kỳ": tien_gui,
                "Tiền lãi kỳ này": tong_lai,
                "Tổng tiền": tong_tien
            })

    # =========================
    # HIỂN THỊ KẾT QUẢ
    # =========================
    st.success("✅ Đã tính toán thành công!")

    st.header("📊 Kết quả")

    col1, col2 = st.columns(2)

    with col1:
        st.markdown(
            f"""
            <div class="result-box">
                <div class="result-label">💵 Tiền lãi định kỳ</div>
                <div class="result-value">
                    {lai_moi_ky:,.0f} VNĐ
                </div>
            </div>
            """,
            unsafe_allow_html=True
        )

    with col2:
        st.markdown(
            f"""
            <div class="result-box">
                <div class="result-label">💰 Tổng tiền lãi</div>
                <div class="result-value">
                    {tong_lai:,.0f} VNĐ
                </div>
            </div>
            """,
            unsafe_allow_html=True
        )

    st.markdown(
        f"""
        <div class="result-box">
            <div class="result-label">🏦 Tổng số tiền gốc + lãi</div>
            <div class="result-value">
                {tong_tien:,.0f} VNĐ
            </div>
        </div>
        """,
        unsafe_allow_html=True
    )

    # =========================
    # THÔNG TIN TÓM TẮT
    # =========================
    st.subheader("📝 Thông tin khoản gửi")

    col1, col2, col3 = st.columns(3)

    with col1:
        st.metric(
            "Tiền gửi",
            f"{tien_gui:,.0f} VNĐ"
        )

    with col2:
        st.metric(
            "Kỳ hạn",
            f"{so_thang} tháng"
        )

    with col3:
        st.metric(
            "Lãi suất",
            f"{lai_suat:.2f}%/năm"
        )

    # =========================
    # BẢNG CHI TIẾT
    # =========================
    st.subheader("📅 Chi tiết theo kỳ")

    df = pd.DataFrame(data)

    # Format tiền
    for column in df.columns:
        if column != "Kỳ":
            df[column] = df[column].apply(
                lambda x: f"{x:,.0f} VNĐ"
            )

    st.dataframe(
        df,
        use_container_width=True,
        hide_index=True
    )

    # =========================
    # CÔNG THỨC
    # =========================
    with st.expander("📚 Xem công thức tính"):

        if loai_lai == "Lãi đơn":

            st.markdown("""
            ### Lãi đơn

            **Công thức tổng tiền lãi:**

            `Tiền lãi = Tiền gốc × Lãi suất năm × Số năm`

            Trong đó:

            - Tiền gốc = số tiền gửi ban đầu
            - Lãi suất năm = lãi suất / 100
            - Số năm = số tháng / 12

            **Tổng tiền nhận được:**

            `Tổng tiền = Tiền gốc + Tiền lãi`
            """)

        else:

            st.markdown("""
            ### Lãi kép

            Tiền lãi của mỗi kỳ được cộng vào số dư,
            sau đó kỳ tiếp theo sẽ tính lãi trên số dư mới.

            **Công thức tổng quát:**

            `A = P × (1 + r/n)^(n×t)`

            Trong đó:

            - `P`: số tiền gốc ban đầu
            - `r`: lãi suất năm
            - `n`: số lần nhập lãi trong một năm
            - `t`: số năm
            - `A`: tổng tiền gốc + lãi

            Vì vậy, **lãi kép tạo ra "lãi trên lãi"**.
            """)
