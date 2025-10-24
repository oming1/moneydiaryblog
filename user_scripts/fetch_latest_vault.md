module.exports = async function(tp) {
  // Dataview API 가져오기
  const dv = app.plugins.plugins["dataview"]?.api;
  if (!dv) return "> ❗ Dataview 플러그인이 활성화되지 않음.";

  // 하드코딩된 폴더 리스트
  const folders = [
    "0.Daily-시황정리",
    "1.산업-테마-Study",
    "2.종목공부",
    "3.Earning(실적)"
  ];

  // 결과를 담을 마크다운 문자열
  let md = "";

  // 각 폴더별로 [!INFO] callout 생성 및 항목 추가
  for (const folder of folders) {
    md += `\n> [!INFO] ${folder}\n`;

    // "0.Daily-시황정리"만 파일명 기준 정렬, 나머지는 수정일 기준 정렬
    const useNameSort = folder === "0.Daily-시황정리";
    const sortFn = useNameSort
      ? (p => p.file.name)
      : (p => p.file.mtime);

    const pages = dv.pages()
      .where(p =>
        p.file.folder.startsWith(folder) &&
        p.file.frontmatter["dg-publish"] === true
      )
      .sort(sortFn, "desc")
      .limit(7);

    // 각 문서를 링크와 날짜 포맷으로 출력
    for (const p of pages) {
      let dateStr;
      if (useNameSort) {
        // 파일명이 YYYY-MM-DD 형식일 때 그대로 사용
        dateStr = p.file.name;
      } else {
        dateStr = dv.date(p.file.mtime).toFormat("yyyy-MM-dd");
      }
      const pathNoExt = p.file.path.replace(/\.md$/, "");
      md += `> - [[${pathNoExt}|${p.file.name}]] (${dateStr})\n`;
    }

    // 폴더별 섹션 구분용 빈 줄
    md += `\n`;
  }

  return md;
};
