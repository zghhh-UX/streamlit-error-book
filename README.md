# streamlit-error-book
import streamlit as st
import pandas as pd
from datetime import datetime

# 页面配置
st.set_page_config(page_title="错题本", page_icon="📝", layout="wide")

# 初始化数据
if "errors" not in st.session_state:
    st.session_state.errors = []

# 标题
st.title("📝 我的错题本")

# 侧边栏：添加新错题
with st.sidebar:
    st.header("添加新错题")
    subject = st.selectbox("科目", ["数学", "语文", "英语", "物理", "化学", "生物", "其他"])
    question = st.text_area("题目内容")
    answer = st.text_area("正确答案")
    error_reason = st.text_area("错误原因/知识点")
    add_btn = st.button("添加到错题本")

    if add_btn and question and answer:
        new_error = {
            "时间": datetime.now().strftime("%Y-%m-%d %H:%M"),
            "科目": subject,
            "题目": question,
            "正确答案": answer,
            "错误原因": error_reason
        }
        st.session_state.errors.append(new_error)
        st.success("✅ 添加成功！")

# 主区域：查看错题
st.subheader("📚 错题列表")
if st.session_state.errors:
    df = pd.DataFrame(st.session_state.errors)
    st.dataframe(df, use_container_width=True)

    # 按科目筛选
    st.subheader("🔍 按科目筛选")
    selected_subject = st.selectbox("选择科目", ["全部"] + list(df["科目"].unique()), index=0)
    if selected_subject != "全部":
        filtered_df = df[df["科目"] == selected_subject]
        st.dataframe(filtered_df, use_container_width=True)

else:
    st.info("还没有添加错题，快去侧边栏添加吧！")

# 导出功能
st.subheader("💾 导出错题本")
if st.session_state.errors:
    csv = pd.DataFrame(st.session_state.errors).to_csv(index=False).encode("utf-8-sig")
    st.download_button(
        label="下载为CSV文件",
        data=csv,
        file_name="错题本.csv",
        mime="text/csv"
    )
else:
    st.warning("没有错题可导出")
