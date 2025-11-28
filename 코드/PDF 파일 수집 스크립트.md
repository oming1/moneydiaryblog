# =================================================================
# Content 폴더 내 모든 PDF 파일 수집 및 복사 스크립트
# =================================================================
#
# 기능:
# 1. content 폴더 내의 모든 PDF 파일을 재귀적으로 검색
# 2. 발견된 PDF 파일을 moneydiary-assets/mian/documents/ 폴더로 복사
# 3. 파일명 중복 시 자동으로 번호 추가 (예: 파일.pdf → 파일_1.pdf)
# 4. 복사 완료 후 통계 출력
#
# =================================================================

# --- 사용자 설정 시작 ---
# =================================================================

# 1. 검색할 content 폴더 경로
$contentPath = "D:\GitHub\moneydiaryblog\content"

# 2. PDF 파일을 복사할 대상 폴더 경로
$targetPath = "D:\GitHub\moneydiary-assets\documents"

# 3. 복사 후 원본 파일 삭제 여부 (기본값: $false - 원본 유지)
$deleteOriginal = $false

# =================================================================
# --- 사용자 설정 끝 ---

Write-Host "========================================" -ForegroundColor Cyan
Write-Host "   PDF 파일 수집 및 복사 스크립트" -ForegroundColor Cyan
Write-Host "========================================" -ForegroundColor Cyan
Write-Host "검색 경로: $contentPath"
Write-Host "대상 경로: $targetPath"
Write-Host "원본 삭제: $(if ($deleteOriginal) { '예' } else { '아니오' })"
Write-Host ""
Write-Host "🚨 경고: 이 작업은 파일을 복사합니다. 신중하게 진행하세요! 🚨" -ForegroundColor Red
Write-Host ""

# 대상 폴더가 없으면 생성
if (-not (Test-Path $targetPath)) {
    Write-Host "대상 폴더가 없습니다. 생성 중..." -ForegroundColor Yellow
    New-Item -Path $targetPath -ItemType Directory -Force | Out-Null
    Write-Host "✓ 대상 폴더 생성 완료: $targetPath" -ForegroundColor Green
    Write-Host ""
}

# 통계 변수
$totalPDFsFound = 0
$totalPDFsCopied = 0
$totalPDFsSkipped = 0
$totalPDFsRenamed = 0
$copiedFiles = @()
$skippedFiles = @()

Write-Host "=== PDF 파일 검색 중... ===" -ForegroundColor Magenta
Write-Host ""

# content 폴더 내 모든 PDF 파일 검색
$pdfFiles = Get-ChildItem -Path $contentPath -Filter "*.pdf" -Recurse -File

$totalPDFsFound = $pdfFiles.Count

if ($totalPDFsFound -eq 0) {
    Write-Host "⚠ PDF 파일을 찾을 수 없습니다." -ForegroundColor Yellow
    Write-Host ""
    exit
}

Write-Host "✓ 총 $totalPDFsFound 개의 PDF 파일을 발견했습니다." -ForegroundColor Green
Write-Host ""
Write-Host "=== PDF 파일 복사 중... ===" -ForegroundColor Magenta
Write-Host ""

foreach ($pdfFile in $pdfFiles) {
    $fileName = $pdfFile.Name
    $sourceFullPath = $pdfFile.FullName
    $targetFullPath = Join-Path $targetPath $fileName
    
    # 상대 경로 표시 (어디서 발견되었는지)
    $relativePath = $sourceFullPath.Replace($contentPath, "").TrimStart('\')
    
    Write-Host "처리 중: $relativePath" -ForegroundColor Cyan
    
    # 파일명 중복 확인
    if (Test-Path $targetFullPath) {
        # 기존 파일과 동일한지 확인 (파일 크기 및 수정 시간 비교)
        $sourceHash = (Get-FileHash -Path $sourceFullPath -Algorithm MD5).Hash
        $targetHash = (Get-FileHash -Path $targetFullPath -Algorithm MD5).Hash
        
        if ($sourceHash -eq $targetHash) {
            Write-Host "  - 건너뜀 (이미 동일한 파일 존재): $fileName" -ForegroundColor DarkGray
            $totalPDFsSkipped++
            $skippedFiles += @{
                FileName = $fileName
                SourcePath = $relativePath
                Reason = "동일한 파일 존재"
            }
            continue
        } else {
            # 파일명이 같지만 내용이 다른 경우 → 번호 추가
            $fileBaseName = [System.IO.Path]::GetFileNameWithoutExtension($fileName)
            $fileExtension = [System.IO.Path]::GetExtension($fileName)
            $counter = 1
            
            while (Test-Path $targetFullPath) {
                $newFileName = "${fileBaseName}_${counter}${fileExtension}"
                $targetFullPath = Join-Path $targetPath $newFileName
                $counter++
            }
            
            Write-Host "  ⚠ 파일명 중복 → 이름 변경: $fileName → $(Split-Path $targetFullPath -Leaf)" -ForegroundColor Yellow
            $totalPDFsRenamed++
        }
    }
    
    # 파일 복사
    try {
        Copy-Item -Path $sourceFullPath -Destination $targetFullPath -Force
        Write-Host "  ✓ 복사 완료: $(Split-Path $targetFullPath -Leaf)" -ForegroundColor Green
        $totalPDFsCopied++
        
        $copiedFiles += @{
            FileName = Split-Path $targetFullPath -Leaf
            SourcePath = $relativePath
            TargetPath = $targetFullPath
        }
        
        # 원본 삭제 옵션이 활성화된 경우
        if ($deleteOriginal) {
            Remove-Item -Path $sourceFullPath -Force
            Write-Host "  ✓ 원본 삭제 완료" -ForegroundColor DarkGreen
        }
    }
    catch {
        Write-Host "  ✗ 복사 실패: $($_.Exception.Message)" -ForegroundColor Red
        $skippedFiles += @{
            FileName = $fileName
            SourcePath = $relativePath
            Reason = "복사 오류: $($_.Exception.Message)"
        }
    }
    
    Write-Host ""
}

# 최종 통계 출력
Write-Host "========================================" -ForegroundColor Magenta
Write-Host "   PDF 파일 수집 완료!" -ForegroundColor Magenta
Write-Host "========================================" -ForegroundColor Magenta
Write-Host "✓ 발견된 PDF 파일: $totalPDFsFound 개" -ForegroundColor Green
Write-Host "✓ 복사된 PDF 파일: $totalPDFsCopied 개" -ForegroundColor Green
Write-Host "✓ 이름 변경된 파일: $totalPDFsRenamed 개" -ForegroundColor Yellow
Write-Host "✓ 건너뛴 PDF 파일: $totalPDFsSkipped 개" -ForegroundColor DarkGray
Write-Host "========================================" -ForegroundColor Magenta
Write-Host ""

# 복사된 파일 목록 출력 (선택사항)
if ($copiedFiles.Count -gt 0) {
    Write-Host "=== 복사된 파일 목록 ===" -ForegroundColor Cyan
    foreach ($file in $copiedFiles) {
        Write-Host "  - $($file.FileName)" -ForegroundColor White
        Write-Host "    원본: $($file.SourcePath)" -ForegroundColor DarkGray
    }
    Write-Host ""
}

# 건너뛴 파일 목록 출력 (선택사항)
if ($skippedFiles.Count -gt 0) {
    Write-Host "=== 건너뛴 파일 목록 ===" -ForegroundColor Yellow
    foreach ($file in $skippedFiles) {
        Write-Host "  - $($file.FileName)" -ForegroundColor White
        Write-Host "    원본: $($file.SourcePath)" -ForegroundColor DarkGray
        Write-Host "    이유: $($file.Reason)" -ForegroundColor DarkGray
    }
    Write-Host ""
}

Write-Host "다음 단계:" -ForegroundColor Cyan
Write-Host "1. D:\GitHub\moneydiary-assets\documents\ 폴더 확인" -ForegroundColor White
Write-Host "2. moneydiary-assets 저장소로 이동" -ForegroundColor White
Write-Host "3. git add documents/" -ForegroundColor White
Write-Host "4. git commit -m 'Add PDF files from content folder'" -ForegroundColor White
Write-Host "5. git push origin main" -ForegroundColor White
Write-Host "========================================" -ForegroundColor Magenta
