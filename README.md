
https://teams.live.com/meet/9368630493462?p=bofYeyYCbV0dnd43EB


from pydantic import BaseModel, Field, validator
from typing import List, Optional, Dict, Any, Union
from enum import Enum

class PresentationStyle(str, Enum):
    """Available presentation styles"""
    PROFESSIONAL = "professional"
    CREATIVE = "creative"
    ACADEMIC = "academic"
    CASUAL = "casual"
    TECHNICAL = "technical"
    MARKETING = "marketing"

class AudienceLevel(str, Enum):
    """Target audience levels"""
    BEGINNER = "beginner"
    INTERMEDIATE = "intermediate"
    ADVANCED = "advanced"
    EXPERT = "expert"
    GENERAL = "general"

class LLMProvider(str, Enum):
    """Available LLM providers"""
    GOOGLE_GEMINI = "gg"
    AZURE_OPENAI = "az"
    TOGETHER_AI = "to"
    COHERE = "co"
    OPENROUTER = "or"
    OLLAMA = "ollama"

# Request Models
class SlideGenerationRequest(BaseModel):
    """Request model for generating slides"""
    session_id: str = Field(..., description="Session ID for tracking")
    topic: str = Field(..., min_length=5, max_length=500, description="Topic for the presentation")
    num_slides: int = Field(5, ge=3, le=20, description="Number of slides to generate")
    llm_provider: LLMProvider = Field(..., description="LLM provider to use")
    api_key: Optional[str] = Field(None, description="API key for the LLM provider")
    model_name: Optional[str] = Field(None, description="Specific model name (for Ollama or custom models)")
    presentation_style: PresentationStyle = Field(PresentationStyle.PROFESSIONAL, description="Style of presentation")
    audience_level: AudienceLevel = Field(AudienceLevel.GENERAL, description="Target audience level")
    template: str = Field("default", description="PowerPoint template to use")
    include_images: bool = Field(True, description="Whether to include relevant images")
    language: str = Field("en", description="Language for the presentation")
    
    @validator('topic')
    def validate_topic(cls, v):
        if len(v.strip()) < 5:
            raise ValueError('Topic must be at least 5 characters long')
        return v.strip()

class SlideRefinementRequest(BaseModel):
    """Request model for refining existing slides"""
    session_id: str = Field(..., description="Session ID")
    instruction: str = Field(..., min_length=5, max_length=1000, description="Instructions for refinement")
    llm_provider: LLMProvider = Field(..., description="LLM provider to use")
    api_key: Optional[str] = Field(None, description="API key for the LLM provider")
    template: Optional[str] = Field(None, description="Change template (optional)")
    update_images: bool = Field(False, description="Whether to update images based on new content")
    
    @validator('instruction')
    def validate_instruction(cls, v):
        if len(v.strip()) < 5:
            raise ValueError('Instruction must be at least 5 characters long')
        return v.strip()

class PDFSlideGenerationRequest(BaseModel):
    """Request model for generating slides from PDF"""
    session_id: str = Field(..., description="Session ID")
    pdf_filename: str = Field(..., description="Name of the uploaded PDF file")
    num_slides: int = Field(5, ge=3, le=20, description="Number of slides to generate")
    llm_provider: LLMProvider = Field(..., description="LLM provider to use")
    api_key: Optional[str] = Field(None, description="API key for the LLM provider")
    presentation_style: PresentationStyle = Field(PresentationStyle.PROFESSIONAL, description="Style of presentation")
    template: str = Field("default", description="PowerPoint template to use")
    include_images: bool = Field(True, description="Whether to include relevant images")

# Response Models
class SessionResponse(BaseModel):
    """Response model for session creation"""
    session_id: str
    message: str

class SessionInfo(BaseModel):
    """Session information model"""
    session_id: str
    created_at: str
    history_count: int
    has_slides: bool
    files_count: int

class SlidePreview(BaseModel):
    """Preview model for a single slide"""
    slide_number: int
    title: str
    content_preview: str
    has_image: bool

class SlideGenerationResponse(BaseModel):
    """Response model for slide generation"""
    success: bool
    message: str
    slide_count: int
    download_url: str
    slides_preview: List[Dict[str, Any]]
    generation_time: Optional[float] = None

class SlideRefinementResponse(BaseModel):
    """Response model for slide refinement"""
    success: bool
    message: str
    slide_count: int
    download_url: str
    changes_summary: str

class PDFUploadResponse(BaseModel):
    """Response model for PDF upload"""
    success: bool
    message: str
    filename: str
    page_count: int
    content_preview: str

class LLMProviderInfo(BaseModel):
    """Information about an LLM provider"""
    name: str
    provider_code: str
    requires_api_key: bool
    characteristics: str
    api_key_url: Optional[str] = None

class TemplateInfo(BaseModel):
    """Information about a PowerPoint template"""
    name: str
    filename: str
    description: str
    preview_url: Optional[str] = None

class ErrorResponse(BaseModel):
    """Standard error response model"""
    error: bool = True
    message: str
    error_code: Optional[str] = None
    details: Optional[Dict[str, Any]] = None

# Internal Models for Slide Content
class SlideContent(BaseModel):
    """Model for individual slide content"""
    slide_number: int
    title: str
    content: List[str]
    speaker_notes: Optional[str] = None
    layout_type: str = "title_and_content"
    image_keywords: Optional[List[str]] = None

class PresentationContent(BaseModel):
    """Model for complete presentation content"""
    title: str
    subtitle: Optional[str] = None
    slides: List[SlideContent]
    metadata: Dict[str, Any] = {}

class ImageSearchResult(BaseModel):
    """Model for image search results"""
    url: str
    title: str
    description: Optional[str] = None
    width: Optional[int] = None
    height: Optional[int] = None
    thumbnail_url: Optional[str] = None

class PDFExtractionResult(BaseModel):
    """Model for PDF content extraction results"""
    text: str
    page_count: int
    images_found: int
    tables_found: int
    metadata: Dict[str, Any] = {}

# Configuration Models
class LLMConfig(BaseModel):
    """Configuration for LLM providers"""
    provider: LLMProvider
    api_key: Optional[str] = None
    model_name: Optional[str] = None
    temperature: float = Field(0.7, ge=0.0, le=2.0)
    max_tokens: int = Field(2000, ge=100, le=8000)
    timeout: int = Field(30, ge=5, le=120)

class ImageSearchConfig(BaseModel):
    """Configuration for image search"""
    enabled: bool = True
    max_images_per_slide: int = Field(1, ge=0, le=3)
    image_quality: str = Field("medium", regex="^(low|medium|high)$")
    safe_search: bool = True
    timeout: int = Field(10, ge=3, le=30)

class SlideGenerationConfig(BaseModel):
    """Configuration for slide generation"""
    template: str = "default"
    include_title_slide: bool = True
    include_end_slide: bool = True
    slide_width: int = Field(10, ge=8, le=16)  # inches
    slide_height: int = Field(7.5, ge=6, le=12)  # inches
    font_size_title: int = Field(24, ge=18, le=32)
    font_size_content: int = Field(18, ge=14, le=24)

# Validation Models
class TopicValidationResult(BaseModel):
    """Result of topic validation"""
    is_valid: bool
    message: str
    suggestions: Optional[List[str]] = None

class APIKeyValidationResult(BaseModel):
    """Result of API key validation"""
    is_valid: bool
    provider: str
    message: str
    rate_limit_info: Optional[Dict[str, Any]] = None

# Batch Processing Models
class BatchSlideGenerationRequest(BaseModel):
    """Request model for batch slide generation"""
    topics: List[str] = Field(..., min_items=1, max_items=10)
    session_id: str
    common_config: SlideGenerationRequest
    
    @validator('topics')
    def validate_topics(cls, v):
        if len(set(v)) != len(v):
            raise ValueError('Topics must be unique')
        return v

class BatchGenerationStatus(BaseModel):
    """Status of batch generation"""
    batch_id: str
    total_topics: int
    completed: int
    failed: int
    in_progress: int
    status: str  # 'pending', 'processing', 'completed', 'failed'
    results: List[Dict[str, Any]] = []

# Analytics Models
class UsageStatistics(BaseModel):
    """Usage statistics for the API"""
    total_sessions: int
    total_slides_generated: int
    total_presentations_downloaded: int
    popular_topics: List[Dict[str, int]]
    provider_usage: Dict[str, int]
    average_slides_per_presentation: float

class GenerationMetrics(BaseModel):
    """Metrics for slide generation performance"""
    generation_time: float
    llm_response_time: float
    image_search_time: float
    presentation_creation_time: float
    total_tokens_used: Optional[int] = None
    cache_hit_rate: Optional[float] = None

# Export all models for easy importing
__all__ = [
    # Enums
    'PresentationStyle', 'AudienceLevel', 'LLMProvider',
    
    # Request Models
    'SlideGenerationRequest', 'SlideRefinementRequest', 'PDFSlideGenerationRequest',
    'BatchSlideGenerationRequest',
    
    # Response Models
    'SessionResponse', 'SessionInfo






  from fastapi import FastAPI, File, UploadFile, HTTPException, Depends, BackgroundTasks
from fastapi.responses import FileResponse, JSONResponse
from fastapi.middleware.cors import CORSMiddleware
from pydantic import BaseModel, Field
from typing import List, Optional, Dict, Any
import os
import json
import asyncio
import aiofiles
import tempfile
import shutil
from pathlib import Path
import logging
from datetime import datetime
import uuid

# Import custom modules (these would be separate files in the actual project)
from llm_providers import LLMProvider, get_llm_provider
from slide_generator import SlideGenerator
from image_search import ImageSearcher
from pdf_processor import PDFProcessor
from models import *

# Configure logging
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

# Initialize FastAPI app
app = FastAPI(
    title="SlideDeck AI API",
    description="Co-create PowerPoint presentations with Generative AI",
    version="2.0.0",
    docs_url="/docs",
    redoc_url="/redoc"
)

# CORS middleware
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],  # Configure based on your frontend domains
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

# Global configuration
UPLOAD_DIR = Path("uploads")
OUTPUT_DIR = Path("outputs")
TEMPLATES_DIR = Path("templates")

# Ensure directories exist
for dir_path in [UPLOAD_DIR, OUTPUT_DIR, TEMPLATES_DIR]:
    dir_path.mkdir(exist_ok=True)

# Global instances
slide_generator = SlideGenerator()
image_searcher = ImageSearcher()
pdf_processor = PDFProcessor()

# In-memory storage for sessions (in production, use Redis or database)
sessions: Dict[str, Dict] = {}

@app.on_event("startup")
async def startup_event():
    """Initialize the application on startup"""
    logger.info("Starting SlideDeck AI FastAPI server...")
    # Initialize any required services
    await image_searcher.initialize()

@app.on_event("shutdown")
async def shutdown_event():
    """Cleanup on shutdown"""
    logger.info("Shutting down SlideDeck AI FastAPI server...")
    # Cleanup temporary files
    for session_id in list(sessions.keys()):
        await cleanup_session(session_id)

@app.get("/")
async def root():
    """Root endpoint with API information"""
    return {
        "message": "SlideDeck AI FastAPI Server",
        "version": "2.0.0",
        "docs": "/docs",
        "health": "/health"
    }

@app.get("/health")
async def health_check():
    """Health check endpoint"""
    return {
        "status": "healthy",
        "timestamp": datetime.now().isoformat(),
        "version": "2.0.0"
    }

@app.post("/sessions", response_model=SessionResponse)
async def create_session():
    """Create a new session for slide generation"""
    session_id = str(uuid.uuid4())
    sessions[session_id] = {
        "created_at": datetime.now().isoformat(),
        "history": [],
        "current_slides": None,
        "files": []
    }
    
    return SessionResponse(
        session_id=session_id,
        message="Session created successfully"
    )

@app.get("/sessions/{session_id}", response_model=SessionInfo)
async def get_session(session_id: str):
    """Get session information"""
    if session_id not in sessions:
        raise HTTPException(status_code=404, detail="Session not found")
    
    session = sessions[session_id]
    return SessionInfo(
        session_id=session_id,
        created_at=session["created_at"],
        history_count=len(session["history"]),
        has_slides=session["current_slides"] is not None,
        files_count=len(session["files"])
    )

@app.delete("/sessions/{session_id}")
async def delete_session(session_id: str):
    """Delete a session and cleanup associated files"""
    if session_id not in sessions:
        raise HTTPException(status_code=404, detail="Session not found")
    
    await cleanup_session(session_id)
    del sessions[session_id]
    
    return {"message": "Session deleted successfully"}

@app.get("/llm-providers", response_model=List[LLMProviderInfo])
async def get_llm_providers():
    """Get available LLM providers and their characteristics"""
    providers = [
        LLMProviderInfo(
            name="Gemini 2.0 Flash",
            provider_code="gg",
            requires_api_key=True,
            characteristics="Fast, efficient, good for general content",
            api_key_url="https://aistudio.google.com/apikey"
        ),
        LLMProviderInfo(
            name="GPT-4o",
            provider_code="az",
            requires_api_key=True,
            characteristics="High quality, detailed content generation",
            api_key_url="https://ai.azure.com/resource/playground"
        ),
        LLMProviderInfo(
            name="Mistral NeMo",
            provider_code="to",
            requires_api_key=True,
            characteristics="Balanced performance and quality",
            api_key_url="https://api.together.ai/settings/api-keys"
        ),
        LLMProviderInfo(
            name="Cohere Command",
            provider_code="co",
            requires_api_key=True,
            characteristics="Business-focused content generation",
            api_key_url="https://dashboard.cohere.com/api-keys"
        ),
        LLMProviderInfo(
            name="OpenRouter",
            provider_code="or",
            requires_api_key=True,
            characteristics="Multiple model access through one API",
            api_key_url="https://openrouter.ai/settings/keys"
        )
    ]
    return providers

@app.post("/generate-slides", response_model=SlideGenerationResponse)
async def generate_slides(
    request: SlideGenerationRequest,
    background_tasks: BackgroundTasks
):
    """Generate slides based on topic and configuration"""
    if request.session_id not in sessions:
        raise HTTPException(status_code=404, detail="Session not found")
    
    session = sessions[request.session_id]
    
    try:
        # Get LLM provider
        llm_provider = get_llm_provider(
            provider_code=request.llm_provider,
            api_key=request.api_key,
            model_name=request.model_name
        )
        
        # Generate slide content using LLM
        logger.info(f"Generating content for topic: {request.topic}")
        slide_content = await llm_provider.generate_slides(
            topic=request.topic,
            num_slides=request.num_slides,
            presentation_style=request.presentation_style,
            audience_level=request.audience_level
        )
        
        # Search for images if enabled
        images = {}
        if request.include_images:
            logger.info("Searching for relevant images...")
            images = await image_searcher.search_images_for_slides(slide_content)
        
        # Create PowerPoint presentation
        logger.info("Creating PowerPoint presentation...")
        pptx_path = await slide_generator.create_presentation(
            slide_content=slide_content,
            images=images,
            template=request.template,
            session_id=request.session_id
        )
        
        # Update session
        session["current_slides"] = slide_content
        session["history"].append({
            "timestamp": datetime.now().isoformat(),
            "action": "generate_slides",
            "topic": request.topic,
            "num_slides": len(slide_content.get("slides", []))
        })
        
        # Schedule cleanup of old files
        background_tasks.add_task(cleanup_old_files, request.session_id)
        
        return SlideGenerationResponse(
            success=True,
            message="Slides generated successfully",
            slide_count=len(slide_content.get("slides", [])),
            download_url=f"/download/{request.session_id}",
            slides_preview=slide_content.get("slides", [])[:3]  # First 3 slides preview
        )
        
    except Exception as e:
        logger.error(f"Error generating slides: {str(e)}")
        raise HTTPException(status_code=500, detail=f"Failed to generate slides: {str(e)}")

@app.post("/refine-slides", response_model=SlideRefinementResponse)
async def refine_slides(
    request: SlideRefinementRequest,
    background_tasks: BackgroundTasks
):
    """Refine existing slides based on user instructions"""
    if request.session_id not in sessions:
        raise HTTPException(status_code=404, detail="Session not found")
    
    session = sessions[request.session_id]
    
    if not session["current_slides"]:
        raise HTTPException(status_code=400, detail="No slides to refine. Generate slides first.")
    
    try:
        # Get LLM provider
        llm_provider = get_llm_provider(
            provider_code=request.llm_provider,
            api_key=request.api_key
        )
        
        # Refine slides based on instructions
        logger.info(f"Refining slides with instruction: {request.instruction}")
        refined_content = await llm_provider.refine_slides(
            current_slides=session["current_slides"],
            instruction=request.instruction
        )
        
        # Update images if needed
        images = {}
        if request.update_images:
            images = await image_searcher.search_images_for_slides(refined_content)
        
        # Create updated presentation
        pptx_path = await slide_generator.create_presentation(
            slide_content=refined_content,
            images=images,
            template=request.template or "default",
            session_id=request.session_id
        )
        
        # Update session
        session["current_slides"] = refined_content
        session["history"].append({
            "timestamp": datetime.now().isoformat(),
            "action": "refine_slides",
            "instruction": request.instruction,
            "num_slides": len(refined_content.get("slides", []))
        })
        
        background_tasks.add_task(cleanup_old_files, request.session_id)
        
        return SlideRefinementResponse(
            success=True,
            message="Slides refined successfully",
            slide_count=len(refined_content.get("slides", [])),
            download_url=f"/download/{request.session_id}",
            changes_summary="Slides updated based on your instructions"
        )
        
    except Exception as e:
        logger.error(f"Error refining slides: {str(e)}")
        raise HTTPException(status_code=500, detail=f"Failed to refine slides: {str(e)}")

@app.post("/upload-pdf", response_model=PDFUploadResponse)
async def upload_pdf(
    session_id: str,
    file: UploadFile = File(...),
    page_range: Optional[str] = None
):
    """Upload and process PDF for slide generation"""
    if session_id not in sessions:
        raise HTTPException(status_code=404, detail="Session not found")
    
    if not file.filename.lower().endswith('.pdf'):
        raise HTTPException(status_code=400, detail="Only PDF files are allowed")
    
    try:
        # Save uploaded file
        file_path = UPLOAD_DIR / f"{session_id}_{file.filename}"
        
        async with aiofiles.open(file_path, 'wb') as f:
            content = await file.read()
            await f.write(content)
        
        # Process PDF
        logger.info(f"Processing PDF: {file.filename}")
        extracted_content = await pdf_processor.extract_content(
            file_path=file_path,
            page_range=page_range
        )
        
        # Update session
        sessions[session_id]["files"].append({
            "filename": file.filename,
            "file_path": str(file_path),
            "upload_time": datetime.now().isoformat(),
            "extracted_content": extracted_content
        })
        
        return PDFUploadResponse(
            success=True,
            message="PDF uploaded and processed successfully",
            filename=file.filename,
            page_count=extracted_content.get("page_count", 0),
            content_preview=extracted_content.get("text", "")[:200] + "..."
        )
        
    except Exception as e:
        logger.error(f"Error processing PDF: {str(e)}")
        raise HTTPException(status_code=500, detail=f"Failed to process PDF: {str(e)}")

@app.post("/generate-from-pdf", response_model=SlideGenerationResponse)
async def generate_slides_from_pdf(
    request: PDFSlideGenerationRequest,
    background_tasks: BackgroundTasks
):
    """Generate slides from uploaded PDF content"""
    if request.session_id not in sessions:
        raise HTTPException(status_code=404, detail="Session not found")
    
    session = sessions[request.session_id]
    
    # Find the PDF file
    pdf_file = None
    for file_info in session["files"]:
        if file_info["filename"] == request.pdf_filename:
            pdf_file = file_info
            break
    
    if not pdf_file:
        raise HTTPException(status_code=404, detail="PDF file not found")
    
    try:
        # Get LLM provider
        llm_provider = get_llm_provider(
            provider_code=request.llm_provider,
            api_key=request.api_key
        )
        
        # Generate slides from PDF content
        logger.info(f"Generating slides from PDF: {request.pdf_filename}")
        slide_content = await llm_provider.generate_slides_from_text(
            text_content=pdf_file["extracted_content"]["text"],
            num_slides=request.num_slides,
            presentation_style=request.presentation_style
        )
        
        # Search for images if enabled
        images = {}
        if request.include_images:
            images = await image_searcher.search_images_for_slides(slide_content)
        
        # Create presentation
        pptx_path = await slide_generator.create_presentation(
            slide_content=slide_content,
            images=images,
            template=request.template,
            session_id=request.session_id
        )
        
        # Update session
        session["current_slides"] = slide_content
        session["history"].append({
            "timestamp": datetime.now().isoformat(),
            "action": "generate_from_pdf",
            "pdf_filename": request.pdf_filename,
            "num_slides": len(slide_content.get("slides", []))
        })
        
        background_tasks.add_task(cleanup_old_files, request.session_id)
        
        return SlideGenerationResponse(
            success=True,
            message="Slides generated from PDF successfully",
            slide_count=len(slide_content.get("slides", [])),
            download_url=f"/download/{request.session_id}",
            slides_preview=slide_content.get("slides", [])[:3]
        )
        
    except Exception as e:
        logger.error(f"Error generating slides from PDF: {str(e)}")
        raise HTTPException(status_code=500, detail=f"Failed to generate slides from PDF: {str(e)}")

@app.get("/download/{session_id}")
async def download_presentation(session_id: str):
    """Download the generated PowerPoint presentation"""
    if session_id not in sessions:
        raise HTTPException(status_code=404, detail="Session not found")
    
    # Find the latest presentation file
    session_dir = OUTPUT_DIR / session_id
    if not session_dir.exists():
        raise HTTPException(status_code=404, detail="No presentation found")
    
    pptx_files = list(session_dir.glob("*.pptx"))
    if not pptx_files:
        raise HTTPException(status_code=404, detail="No presentation file found")
    
    # Get the most recent file
    latest_file = max(pptx_files, key=lambda f: f.stat().st_mtime)
    
    return FileResponse(
        path=latest_file,
        filename=f"slidedeck_ai_{session_id}.pptx",
        media_type="application/vnd.openxmlformats-officedocument.presentationml.presentation"
    )

@app.get("/templates", response_model=List[TemplateInfo])
async def get_templates():
    """Get available PowerPoint templates"""
    templates = []
    
    # Scan templates directory
    for template_file in TEMPLATES_DIR.glob("*.pptx"):
        templates.append(TemplateInfo(
            name=template_file.stem,
            filename=template_file.name,
            description=f"Template: {template_file.stem.replace('_', ' ').title()}"
        ))
    
    # Add default templates if none found
    if not templates:
        templates = [
            TemplateInfo(
                name="default",
                filename="default.pptx",
                description="Default clean template"
            ),
            TemplateInfo(
                name="business",
                filename="business.pptx",
                description="Professional business template"
            ),
            TemplateInfo(
                name="academic",
                filename="academic.pptx",
                description="Academic presentation template"
            )
        ]
    
    return templates

@app.get("/session-history/{session_id}", response_model=List[Dict[str, Any]])
async def get_session_history(session_id: str):
    """Get the history of actions for a session"""
    if session_id not in sessions:
        raise HTTPException(status_code=404, detail="Session not found")
    
    return sessions[session_id]["history"]

# Utility functions
async def cleanup_session(session_id: str):
    """Clean up files associated with a session"""
    try:
        # Remove uploaded files
        for file_info in sessions.get(session_id, {}).get("files", []):
            file_path = Path(file_info["file_path"])
            if file_path.exists():
                file_path.unlink()
        
        # Remove output files
        session_dir = OUTPUT_DIR / session_id
        if session_dir.exists():
            shutil.rmtree(session_dir)
            
    except Exception as e:
        logger.error(f"Error cleaning up session {session_id}: {str(e)}")

async def cleanup_old_files(session_id: str, keep_latest: int = 3):
    """Keep only the latest N files and remove older ones"""
    try:
        session_dir = OUTPUT_DIR / session_id
        if not session_dir.exists():
            return
        
        pptx_files = sorted(
            session_dir.glob("*.pptx"),
            key=lambda f: f.stat().st_mtime,
            reverse=True
        )
        
        # Remove old files, keep only the latest ones
        for old_file in pptx_files[keep_latest:]:
            old_file.unlink()
            
    except Exception as e:
        logger.error(f"Error cleaning up old files for session {session_id}: {str(e)}")

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(
        app,
        host="0.0.0.0",
        port=8000,
        reload=True,
        log_level="info"
    )  
