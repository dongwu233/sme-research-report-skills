# SME Research Report Skill

一个 Claude Code 技能插件，说一句"调研一下 XX 公司"即可自动生成结构化的公司调研简报（HTML + PNG 长截图结果生成）。

**适用场景：** 求职者调研企业信息，尤其是中心企业/初创企业；同理，企业可以通过调研简报向求职者快速呈现企业核心公开信息

**输出内容：** 核心结论、公司概况、企业资质、主营业务、团队组织、市场表现、融资背景、发展策略，共 7 节。不只罗列数据，每条都会告诉你"这意味着什么"。

**安装：** `claude skills install /path/to/sme-research-report.skill`。PNG 截图需 `npm install -g puppeteer-core` + Chrome 浏览器。
